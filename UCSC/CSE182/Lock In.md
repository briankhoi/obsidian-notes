## Practice Midterm
```SQL
1. 25
2. 10 * 2^(2+3+4) (wrong only PK matters dont need to include D)
3. 
	1. n/a
4. Atomictiy is all statements in a transaction succeed or none do; important because we don''t want to have a series of statements and have a failure in between leaving a partial statement completion like a bank transfer.
5. EMPTY SET, NULL, UNKNOWN
6. 
	1. Team, Opponent
	2. Tigers, Bay Stars
	3. Tigers, Bay Stars
	4. Giants, Carp
	5. Giants, Swallows
7. 
	1. Day, theSum
	2. Monday, 3
	3. Sunday, 10
8. 
	1. Day, Team
	2. Monday, Bay Stars
	3. Sunday, Swallows
	4. Sunday, Tigers
9. FALSE
10. TRUE
11. FALSE
12. TRUE
13. TRUE (WRONG it was false)
14. .
SELECT DISTINCT p.patientID, p.patientName
FROM Patients p, Visits v, Dentists d
WHERE p.patientID = v.patientID
	AND d.dentistID = v.dentistID
	AND d.dentistName LIKE '%smith'
	AND p.creditCardType IS NOT NULL
	AND p.expirationDate = DATE '2023-12-18';

15.
SELECT t.toothNumber AS theToothNumber, t.toothName AS theToothName
FROM Teeth t, TreatmentsDuringVisit tdv
WHERE NOT tdv.wasPaymentReceived
	AND
```