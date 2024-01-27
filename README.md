# Bank_Fraud_Detection_SQL
Explanation: In this Project, I have created dummy data for detecting fraud from bank database system. I have used seven enitities- Customer, Account, Merchant Trancsaction, Card, Merchants, Alerts, Case.
● Customer - This entity represents the bank's customers and includes
information.
● Account - The Account entity is used to store details about bank accounts.
● Merchant Transaction - This entity tracks individual transactions associated
with bank accounts and an external merchant.
● Card - The Card entity manages information related to credit/debit cards issued
to customers.
● Merchants - Represents information about merchants involved in transactions.
They are not the customers of the bank (account holders).
● Alerts - The Alert entity is used to store information about fraud alerts triggered
by suspicious activities.
● Case - Every Case has some transactions that stem from a same investigation.
An investigator is assigned to the case


I have used Google colab and python to load the dummy datasets beforehand for making sure the dataset is appropriate and visually correct.

Using this datasets some anomolies cases are given below: 
**Anomaly-1**
Description - Finding suspicious transactions by searching for transactions made more than $5000 in one day.

SQL Command:
SELECT
GROUP_CONCAT(MerchantTransactionID) AS
TransactionIDs,FromAccountNumber,
MerchantID,
DATE(TransactionDate) AS
TransactionDate,
GROUP_CONCAT(Amount) AS AmountList
FROM MerchantTransaction
WHERE Amount > 5000
GROUP BY DATE(TransactionDate),
FromAccountNumber,MerchantID
HAVING COUNT(*) >= 2;

**Anomaly-2**
Description - Sending alert message for suspicious transactions to the Merchant.

SQL Command:
CREATE TABLE MerchantTransaction
(MerchantTransactionID INT PRIMARY KEY,
From AccountNumber BIGINT,
Amount DECIMAL(10, 2),
TransactionDate DATETIME,
TransactionType VARCHAR(50),
MerchantID INT(100),
FOREIGN KEY (MerchantID)
REFERENCES Merchant(MerchantID),
FOREIGN KEY (FromAccountNumber)
REFERENCES Account(AccountNumber)
);
SELECT * FROM MerchantTransaction
WHERE Amount>=5000;

**Anomaly-3**
Description - Finding Duplicate Transaction made by Merchant with amount, type and date.

SQL Command:
SELECT MerchantTransactionID,
From AccountNumber,Amount,Transaction
Date,TransactionType,MerchantID
FROM MerchantTransaction mt1
WHERE
Amount IN (SELECT Amount FROM
MerchantTransaction mt2
WHERE mt2.MerchantTransactionID
<> mt1.MerchantTransactionID
AND mt2.TransactionDate BETWEEN
DATE_SUB(mt1.TransactionDate,
INTERVAL 1 MINUTE)AND
DATE_ADD(mt1.TransactionDate,
INTERVAL 1 MINUTE) );

**Anomaly-4**
Description - Finding no.of Alerts for different customer accounts in order to detect fraud.

SQL Command:
SELECT c.CustomerID,
COUNT(mta.AlertID) AS AlertCount FROM
Customer c
LEFT JOIN
Account a ON c.CustomerID =
a.CustomerID
LEFT JOIN MerchantTransaction mt ON
a.AccountNumber = mt.FromAccountNumber
LEFT JOIN
MerchantTransactionAlert mta ON
mt.MerchantTransactionID =
mta.MerchantTransactionID
GROUP BY c.CustomerID
ORDER BY AlertCount DESC;


Tools used for this project: VS studio, MySQL Workbench, Python, Google Colab.

Since the goal of this project was to implement SQL commands only for the main output, I have not used python for doing future modifications like- machine learning to predict if the fraud is positive, we can freeze the customer Cards and take actionable case on the Merchant.




