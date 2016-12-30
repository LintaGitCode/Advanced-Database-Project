# Advanced-Database-Project
Graduate program(MS in Information Systems) project
IS 620 Advanced Database Projects
Fall 2016
Group Project
XYZ Utility Company’s Account Management Project
Overview
You will form groups of two to five people in this project. It is recommended that each group consists of at least three people given the amount of work. Please read the whole document carefully before starting your project.
Your assignment is to design an account management database for a XYZ utility company. You will design the database, insert some sample data, and implement a set of required features. Each feature will be implemented as one or more Oracle PL/SQL procedures. You do NOT need to write a graphic user interface. 

Assumptions:
You can make the following assumptions in this project. 
1.	The system needs to store data about customers (you can assume that each customer needs to register with the system). Each customer has customer ID (for internal use), name, mailing address (including street, city, and zipcode), phone number, email, and a password (email is used as username).
mailing address has different table ??? is it same as the rented house address ???

Insert statement – pl/sql
From the prompt

2.	The system also stores information about houses. Each house has an address, zip code, and city.

3.	Each customer can own one or more accounts. Each account has an account number, account balance, and a house linked to the account. Each account also has a start date of service and end date of service (could be null, meaning it is still in service), and an account status (active or inactive).

4.	The Monthly bill table stores monthly bills for each account. The table stores account number, year and month, electricity usage (in kw), gas usage (in therm), previous balance, total charge.
5.	The system also stores a rate table that contains the current unit charge for electricity ($ per kw) and gas ($ per cubic feet), and a flat fee. These charges may change over time so you need to store the year and month of these charges. 
6.	The system has an outage table that stores power outage. It has an outage ID, start and end time, status (active or resolved), zipcode (you can assume that outages in the same zipcode and on the same date is the same outage) and a list of houses affected.  ?????????????????????????????????
7.	Each customer can report a service problem. A ticket will be generated for the problem, with a ticket ID, associated account number, type of problem, problem description, report date, status (active or resolved), service charge, and when the problem is fixed. 
8.	The system stores a message table for accounts. It stores a message ID, account ID, message body, and message date.
9.	The system stores payment history for each customer to each account.
Features: 
Features for account management:

1. Register a customer with the system. The customer needs to provide name, address, phone#, email, password. The procedure should check whether the email already exists in user table. If so, please print a message saying the customer exists. Otherwise create an account with input values and return a new customer ID. - I

2. Allow a customer to login by providing email and password. Please check whether email exists and password matches. If not, please print a message to indicate the error. Otherwise print a message to indicate user has logged on. The procedure should return a value 1 for success login and 0 for unsuccessful log in. – I 

3. Allow a customer to read messages providing customer id and a starting date. Print out messages for that customer since that date. – I 

4. Allow a customer to start service on an address. The input includes a house ID, a start date, and customer ID. You need to create an account for the customer on the house if the customer does not have an account on that house earlier (otherwise the old account will be used). A flat connection fee will be also charged. Insert a message to message table stating service is scheduled to start on the start date and new account number. 

5. Allow a customer to end service on an address. The input includes an account ID and an end date. You need to record the end date in the account. A flat disconnection fee will be also charged to the account.  Note that you should set the account status to inactive in feature 6 (when the bill for the final month is generated). Please also insert a message to the message table stating end service request received. 


Features for billing and payment

Total_charge = usage*rate + prev_balance
Account_balance = total_charges

6. Generate monthly bill for an account. The input includes: account number, year, month, electricity usage, gas usage, bill generation date. Please first compute the total charge for that month, then update the account balance to the new total charge, record the usage information, and print out customer’s name, address, account#, previous balance (before the update), electricity usage and charge, gas usage and charge, and total charge. Please also set a due date which is 15 days from the bill generation date. For example, if the bill is generated on April 2nd 2016 for March 2016’s charge, the due date is April 17th 2016. Insert a message into message table stating bill is ready. You need to consider the following cases:
•	Normal case when the customer does not start or end service???on this house in this month: total charge equals previous balance + electricity usage * unit charge for electricity + gas usage * gas unit charge + flat fee). 
•	The customer just started service on this account in the given month, you need to prorate the bill if another customer ends service in the same month on the same house (e.g., when the first customer purchased the house from the second customer). For example, suppose a customer A starts service on a house on April 16th 2016, and another customer B ends service on the same house on April 15th 2016. Customer A will be responsible for 15/30 = 50% of electricity and gas usage and Customer B will be responsible for the rest. Please also set the account that ended service in that month to be inactive. 
•	You can assume that when this feature is called, both the customer who starts and service and the customer who ends the service have entered their request. So if there is a start service request but no end service request, you can assume that the house has no service previously so the customer is still responsible for whole usage (normal case).
•	The case for a customer who ends the service in that month is similar. If you can find another customer who starts service in that month on the same house, the first customer is only responsible for part of the usage (prorated by number of days). If there is no start service calls, the customer is responsible for all usage. ??????

Hint: to compute the number of days, you can use last_day function in Oracle (it returns last day of a month of a given date) and extract function (it can extract number of days from a date type). E.g., given a date '2016-1-10', extract (day from last_day(date '2016-1-10')) gives you 31 (#of days in that month), extract (day from date '2016-1-10') gives you 10 (the number of days since first day of month. 


7. Allow a customer to pay a certain amount to an account. The input includes account ID, payment amount, payment method (credit card or check). Please generate a payment record in payment history table including account ID, payment amount, payment method, and payment date (current date). The balance of the account will be reduced by that amount. Please also insert a message into message table stating payment received and payment amount. 

8. Check for past due account and charge a late fee. There is no input. The feature checks for each account and see whether the balance is still positive if the current date is after the due date of the most recent monthly bill. If so, the account is past due and a 5% late charge is added to the account balance. Please also insert a message into message table stating that the account is past due. 

Features for service calls and outage report

9. Allow a customer to report a service problem other than power outage (which is handled in feature 11). The input includes account ID, problem description, and report date. Generate a ticket ID and insert a row into ticket table.  Set the status to be “in progress”. Insert a message to the message table stating a ticket is generated. 

10.	Mark a service problem being resolved. The input includes ticket ID, service charge, and service date. Please update the status to “resolved” and add the service charge to the account balance. Insert a message to the message table stating the ticket is resolved. 
11.	 Allow a customer to report an outage. The input is house ID and date and time of the outage. The system first checks whether there is an existing outage reported in the same zip code and on the same date. If so it is not a new outage and the feature simply adds the house ID to the list of house IDs of the outage ????????????(ask professor)
if it is not inserted yet. Otherwise, it is a new outage so please generate a new outage ID and record the information. A message will be inserted into message table stating that outage report received. 

12. Allow the system to report that an outage has been resolved. Update the outage status to resolved, record the resolved time. Insert a message to the message table for each account involved stating that the outage has been resolved. 

Features for data analysis:

13. Compute for a given zip code, the average electricity usage and the average usage of the bottom 20% of houses ????? ask professor. Do the same for gas usage as well. Hint: you need to sort the houses by usage and identify the first 20%.

14. Report outage statistics. The input is a start time and end time. Report the number of outages whose start time falls in the time period, the total number of houses affected, the average number of houses affected per outage, the zip code with the most number of outages, and the average duration of outage (computed from the start time to the resolved time). 

15. Do a comparative study for a customer. The input is the account number, the year and month. Report the electricity and gas usage of the customer for that month, the usage of the customer for the same month last year, and the average usage of all customers in the same zip code, the average usage of the bottom 20% of houses in the same zip code. You can call feature 13 to get the average usage and bottom 20% usage. 
