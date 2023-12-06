# Library-Management-System

## HONOR CODE:
 
I pledge, in my honor, to uphold UT Arlington's tradition of academic integrity, a tradition that values hard work and honest effort in the pursuit of academic excellence. 
 
I promise that I will submit only work that I personally create or that I contribute to group collaborations, and I will appropriately reference any work from other sources. I will follow the highest standards of integrity and uphold the spirit of the Honor Code. 



## TASK 1: Execute the following queries on the LMS database tables: 

 
**Query 1: Add an extra column ‘Late’ to the Book_Loan table. Values will be 0-for non-late returns, and 1-for late returns. Then update the ‘Late’ column with '1' for all records that have a return date later than the due date and with '0' for those were returned on time.**
 
Add an extra column ‘Late’ 

    ALTER TABLE BOOK_LOANS 
    ADD Late INTEGER; 
 
Update Late with ‘1’ for all the records that have return date later than the due date and with ‘0’ for the those were on time. 

    UPDATE BOOK_LOANS 
    SET LATE = CASE 
    WHEN Returned_date > Due_date THEN 1 
    ELSE 0 
    END;

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">

**Query 2: Add an extra column ‘LateFee’ to the Library_Branch table, decide late fee per day for each branch and update that column.**
 
Add and extra column ‘LateFee’ to Library_Branch

    ALTER TABLE LIBRARY_BRANCH 
    ADD LateFee INTEGER; 


    UPDATE LIBRARY_BRANCH 
    SET LateFee =  
    CASE Branch_Id 
    WHEN 1 THEN 1 
    WHEN 2 THEN 2 
    WHEN 3 THEN 3 
    WHEN 4 THEN 4 
    WHEN 5 THEN 5 
    ELSE 0  
    END; 


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateFee.png" width = "600" height = "500">


**Query 3: Create a view vBookLoanInfo that retrieves all information per book loan. The view should have the following attributes:**

*Card_No, 
*Borrower Name 
*Date_Out, 
*Due_Date, 
*Returned_date 
*Total Days of book loaned out as 'TotalDays'– you need to change weeks to days
*Book Title 
*Number of days later return – if returned before or on due_date place zero 
*Branch ID 
*Total Late Fee Balance 'LateFeeBalance' – If the book was not retuned late 
 than fee = ‘0’


    CREATE VIEW vBookLoanInfo AS 
    SELECT bl.Card_No, b.Name AS BorrowerName, bl.Date_Out, bl.Due_Date, bl.Returned_date, 
    (julianday(bl.Returned_date) - julianday(bl.Date_Out)) AS TotalDays,  
    bo.Title AS BookTitle,  
    CASE  
    WHEN bl.Returned_date > bl.Due_Date 
    THEN 
    (julianday(bl.Returned_date) - julianday(bl.Due_Date)) 
    ELSE 0  
    END AS DaysLate,  
    bl.Branch_Id,  
    CASE  
    WHEN bl.Returned_date > bl.Due_Date 
    THEN 
    (julianday(bl.Returned_date) - julianday(bl.Due_Date)) * lb.LateFee  
    ELSE 0  
    END AS LateFeeBalance 
    FROM BOOK_LOANS bl 
    JOIN BORROWER b ON bl.Card_No = b.Card_No 
    JOIN BOOK bo ON bl.Book_Id = bo.Book_Id 
    JOIN LIBRARY_BRANCH lb ON bl.Branch_Id = lb.Branch_Id; 



<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/BookLoanInfo.png" width = "600" height = "500">

## Task 2: Create a GUI for the LMS database: 
  
## LMS Application: 


**Create a simple and friendly GUI interface that would be able to perform the following tasks. You may use JAVA programming using JDBC, or C/C++/C# programming with ODBC/Oracle or Python programming or PHP/MySQL or other programming languages to develop a GUI interface. For each query, you need to provide some info to the user about the query's purpose. The user will have to type or select the query’s input parameters and post the question to your program. The program needs to return all result’s rows.** 

**Requirements:**  


**1. User checks out a book, add it to Book_Loan, the number of copies needs to be updated in the Book_Copies table. Show the output of the updated Book_Copies.**


Add book to the BOOK_LOAN 


    submit_cur.execute("INSERT INTO BOOK_LOANS(Book_Id, Branch_Id, Card_no, 	
	
    Date_Out, Due_Date) VALUES (:book_id, :branch_id, :card_no, :date_out, 	
	
    :due_date)", 
    { 
    'book_id': book_id, 
    'branch_id': branch_id, 
    'card_no': card_no, 
    'date_out': date_out, 
    'due_date': due_date, 
    }) 
	
Update Book_Copies 
 
    submit_cur.execute("UPDATE BOOK_COPIES SET No_Of_Copies = No_Of_Copies - 1 WHERE Book_Id = :book_id AND Branch_Id = :branch_id", 
    { 
    'book_id': book_id, 
    'branch_id': branch_id, 
    }) 
 
Show Book_Copies 
 
    submit_cur.execute("SELECT * FROM BOOK_COPIES") 
 


Result:


The user will be prompted to enter following information: Branch ID, Book Id, Book Id, Card No. The system then generates the Date Out and Due Date by adding 1 month to the Date Out. Then, these information are used to add new entries to the Book_Loan Table. The Book_Copies tables is then updated to reflect the new loan.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">

Upon hitting Submit in above window, a screen with updated Book_Copies will be displayed.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


**2. Add information about a new Borrower. Do not provide the CardNo in your query. Output the card number as if you are giving a new library card. Submit your editable SQL query that your code executes.**
 
Add a new Borrower 


    submit_cur.execute("INSERT INTO BORROWER(Name, Address, Phone) VALUES (:name, :address, :phone)", 
    { 
    'name': name, 
    'address': address, 
    'phone': phone, 
    }) 
 
 
	
Output the card number of a new Borrower 
 
    submit_cur.execute("SELECT Card_no FROM BORROWER WHERE Name = :name AND Address = :address AND Phone = :phone", 
    { 
    'name': name, 
    'address': address, 
    'phone': phone, 
    }) 
 
 
Result:
The user will be prompted to enter following information: Name, Address, Phone number. Then provided information is used to add a new entry to the BORROWER table.


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">

Upon pressing Submit, a screen with Card_no assigned to the new Borrower will be displayed.


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">
 
**3. Add a new Book with publisher (use can use a publisher that already exists) and author information to all 5 branches with 5 copies for each branch. Submit your editable SQL query that your code executes.**
 
Add a new book 
 
    submit_cur.execute("insert into BOOK(Title, Book_Author) values (:title, :author)", 
    { 
    'title': title, 
    'author': author, 
    }) 




Add Book to all 5 branches with 5 copies for each branch.

    for branch_id in range(1, 6):
        submit_cur.execute("INSERT INTO BOOK_COPIES (Book_Id, Branch_Id, No_of_Copies) VALUES (:book_id, :branch_id, 5)", 
        {
            'book_id': book_id,
            'branch_id': branch_id
        })


Result: The use is prompted for the following information: Title, Author, Publisher. Then these informations are used to add a new book to BOOK table and Add copies to all branches.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">

Upon pressing Submit button, a new screen with updated Book Table will be displayed.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


**4. Given a book title list the number of copies loaned out per branch.** 

Number of copies loaned out per branch

    submit_cur.execute("SELECT BOOK.Title, LIBRARY_BRANCH.Branch_Name, COUNT(BOOK_LOANS.Book_Id) FROM LIBRARY_BRANCH LEFT JOIN BOOK_LOANS ON LIBRARY_BRANCH.Branch_Id = BOOK_LOANS.Branch_Id NATURAL JOIN BOOK WHERE BOOK.Book_Id = :book_id AND BOOK_LOANS.Returned_date IS NULL GROUP BY LIBRARY_BRANCH.Branch_Name", 
    { 
    'book_id': records[0], 
    }) 

 
Result

The user is prompted for the following information: Book Title. Then the query is run to list the number of copies loaned out per branch.


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


Upon pressing Submit button, a screen with information about he specified book will be displayed. If the specified books are not being loaned out from any branch, no output will be displayed.


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">



**5. Given any due date range list the Book_Loans that were returned late and how many days they were late. Submit your editable SQL queries that your code executes.** 

Given any due date range list the Book_Loans that were returned late and how many days they were late.

    SELECT 
        BOOK_LOANS.Book_Id,
        BOOK_LOANS.Card_No,
        BOOK_LOANS.Date_Out,
        BOOK_LOANS.Due_Date,
        BOOK_LOANS.Returned_date,
        BOOK_LOANS.Late,
        (julianday(BOOK_LOANS.Returned_date) - julianday(BOOK_LOANS.Due_Date)) AS Days_Late
    FROM 
        BOOK_LOANS
        JOIN BORROWER ON BOOK_LOANS.Card_No = BORROWER.Card_No
    WHERE 
        BOOK_LOANS.Due_Date BETWEEN :start_date AND :end_date
        AND BOOK_LOANS.Returned_date > BOOK_LOANS.Due_Date
    ORDER BY 
        Days_Late DESC;
    
    submit_cur.execute(query, {'start_date': due_date1, 'end_date': due_date2})

Result


The user will be prompted for start and end date. Then the provided dates are used to run the query.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


Upon pressing Submit, a screen with detailed information of all the loaned-out books will be displayed.

<img src = "hhttps://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


**6. The fifth requirement is to return the view’s results by applying the following criteria:**

   a. List for every borrower the ID, name, and if there is any lateFee balance. The user has the right to search either by a borrower ID, name, part of the name, or to run the query with no filters/criteria. The amount needs to be in US dollars. For borrowers with zero (0) or NULL balance, you need to return zero dollars ($0.00). Make sure that your query returns meaningful attribute names. In the case that the user decides not to provide any filters, order the results based on the balance amount. Make sure that you return all records. Submit your editable SQL query that your code executes.  
 

    query = """
        SELECT 
            Card_No, 
            BorrowerName, 
            CASE
                WHEN LateFeeBalance IS NULL OR LateFeeBalance = 0 THEN '$0.00'
                ELSE '$' || printf("%.2f", LateFeeBalance)
            END AS LateFeeBalance
        FROM 
            vBookLoanInfo
        WHERE 
            (:search_string IS NULL 
            OR :search_string = '' 
            OR 
            Card_No LIKE '%' || :search_string || '%' OR 
            BorrowerName LIKE '%' || :search_string || '%')
        ORDER BY 
            LateFeeBalance DESC;
        """
        submit_cur.execute(query, {'search_string': borrower_info})


Result
The user is prompted for the following information: Card_no, or Name, or Part of their name. Then based on the information provided a query to display a detailed information about their books they have checkout and late fees will be displayed.


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


Upon pressing Submit button, a screen with detailed information will be displayed.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">



  b. List book information in the view. The user has the right either to search by the book id, books title, part of book title or to run the query with no filters/criteria. The late fee amount needs to be in US dollars. The late fee price amount needs to have two decimals as well as the dollar ‘$’ sign. For books that do not have any late fee amount, you need to substitute the NULL value with a ‘Non-Applicable’ text. Make sure that your query returns meaningful attribute names. In the case that the user decides not to provide any filters, order the results based on the highest late fee remaining. Submit your editable SQL query that your code executes.


    query = """
        SELECT 
        b.Book_Id, 
        b.Title, 
        v.TotalDays,
        v.BorrowerName,  
        CASE 
            WHEN v.LateFeeBalance IS NULL THEN 'Non-Applicable'
            ELSE '$' || printf("%.2f", v.LateFeeBalance)
        END AS LateFeeAmount
        FROM 
            vBookLoanInfo v 
            JOIN BOOK b ON v.BookTitle = b.Title
        WHERE 
            b.Title LIKE '%' || :search_input || '%' 
            OR b.Book_Id = :search_input
        ORDER BY 
            v.LateFeeBalance DESC NULLS LAST
        """


        submit_cur.execute(query, {'search_input': book_info})


	
	
Result
The user will be prompted for the following information: Book_id, Title of the book, or Par of the book title. Using the provided info above query is run.


<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


Upon pressing Submit button, a new screen with the detailed book information will be displayed.

<img src = "https://github.com/waasabbi/Library-Management-System/blob/main/LateColumn.png" width = "600" height = "500">


This is the home Screen of the Library Management System


