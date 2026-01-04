CREATE DATABASE LibraryDB12;

USE LibraryDB12;

--creating tables

--Library Table
CREATE TABLE Library(
    LibraryID INT IDENTITY(1,1) PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Location VARCHAR(150) NOT NULL,
    ContactNumber VARCHAR(20) NOT NULL,
    EstablishedYear INT,

    CONSTRAINT uq_library_name UNIQUE (Name)
);



--Staff Table
CREATE TABLE Staff (
    StaffID INT IDENTITY(1,1) PRIMARY KEY,
    FullName VARCHAR(100) NOT NULL,
    Position VARCHAR(50) NOT NULL,
    ContactNumber VARCHAR(20) NOT NULL,
    LibraryID INT NOT NULL,

    CONSTRAINT fk_staff_library
    FOREIGN KEY (LibraryID) REFERENCES Library(LibraryID)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);



--Book Table
CREATE TABLE Book (
    BookID INT IDENTITY(1,1) PRIMARY KEY,
    ISBN VARCHAR(20) NOT NULL,
    Title VARCHAR(150) NOT NULL,
    Genre VARCHAR(20) NOT NULL,
    Price DECIMAL(10,2) NOT NULL,
    ShelfLocation VARCHAR(50) NOT NULL,
    IsAvailable BIT NOT NULL DEFAULT 1,
    LibraryID INT NOT NULL,

    CONSTRAINT uq_book_isbn UNIQUE (ISBN),
    CONSTRAINT chk_book_genre 
        CHECK (Genre IN ('Fiction','Non-fiction','Reference','Children')),
    CONSTRAINT chk_book_price 
        CHECK (Price > 0),

    CONSTRAINT fk_book_library
    FOREIGN KEY (LibraryID) REFERENCES Library(LibraryID)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);


--Member Table
CREATE TABLE Member (
    MemberID INT IDENTITY(1,1) PRIMARY KEY,
    FullName VARCHAR(100) NOT NULL,
    Email VARCHAR(100) NOT NULL,
    PhoneNumber VARCHAR(20),
    MembershipStartDate DATE NOT NULL,

    CONSTRAINT uq_member_email UNIQUE (Email)
);


--Loan Table
CREATE TABLE Loan (
    LoanID INT IDENTITY(1,1) PRIMARY KEY,
    LoanDate DATE NOT NULL,
    DueDate DATE NOT NULL,
    ReturnDate DATE NULL,
    Status VARCHAR(20) NOT NULL DEFAULT 'Issued',
    MemberID INT NOT NULL,
    BookID INT NOT NULL,

    CONSTRAINT chk_loan_status 
        CHECK (Status IN ('Issued','Returned','Overdue')),
    CONSTRAINT chk_return_date 
        CHECK (ReturnDate IS NULL OR ReturnDate >= LoanDate),

    CONSTRAINT fk_loan_member
    FOREIGN KEY (MemberID) REFERENCES Member(MemberID)
    ON DELETE CASCADE
    ON UPDATE CASCADE,

    CONSTRAINT fk_loan_book
    FOREIGN KEY (BookID) REFERENCES Book(BookID)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);



--Payment Table
CREATE TABLE Payment (
    PaymentID INT IDENTITY(1,1) PRIMARY KEY,
    PaymentDate DATE NOT NULL,
    Amount DECIMAL(10,2) NOT NULL,
    Method VARCHAR(50),
    LoanID INT NOT NULL,

    CONSTRAINT chk_payment_amount 
        CHECK (Amount > 0),

    CONSTRAINT fk_payment_loan
    FOREIGN KEY (LoanID) REFERENCES Loan(LoanID)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);


--Review Table
CREATE TABLE Review (
    ReviewID INT IDENTITY(1,1) PRIMARY KEY,
    Rating INT NOT NULL,
    Comments NVARCHAR(500) DEFAULT 'No comments',
    ReviewDate DATE NOT NULL,
    MemberID INT NOT NULL,
    BookID INT NOT NULL,

    CONSTRAINT chk_review_rating 
        CHECK (Rating BETWEEN 1 AND 5),

    CONSTRAINT fk_review_member
    FOREIGN KEY (MemberID) REFERENCES Member(MemberID)
    ON DELETE CASCADE
    ON UPDATE CASCADE,

    CONSTRAINT fk_review_book
    FOREIGN KEY (BookID) REFERENCES Book(BookID)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);




