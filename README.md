import java.util.ArrayList;
import java.util.Scanner;

/**
 * Represents a Book in the library.
 */
class Book {
    private int bookID;
    private String title;
    private String author;
    private boolean isAvailable;

    // Constructor
    public Book(int bookID, String title, String author) {
        this.bookID = bookID;
        this.title = title;
        this.author = author;
        this.isAvailable = true; // default when book is created
    }

    // Getters & Setters
    public int getBookID() {
        return bookID;
    }

    public String getTitle() {
        return title;
    }

    public String getAuthor() {
        return author;
    }

    public boolean isAvailable() {
        return isAvailable;
    }

    public void setAvailable(boolean available) {
        isAvailable = available;
    }

    @Override
    public String toString() {
        return "BookID: " + bookID +
                ", Title: " + title +
                ", Author: " + author +
                ", Available: " + (isAvailable ? "Yes" : "No");
    }
}

/**
 * Represents a Borrower in the library system.
 */
class Borrower {
    private int borrowerID;
    private String name;
    private ArrayList<Book> borrowedBooks;

    // Constructor
    public Borrower(int borrowerID, String name) {
        this.borrowerID = borrowerID;
        this.name = name;
        this.borrowedBooks = new ArrayList<>();
    }

    // Getters
    public int getBorrowerID() {
        return borrowerID;
    }

    public String getName() {
        return name;
    }

    public ArrayList<Book> getBorrowedBooks() {
        return borrowedBooks;
    }

    // Borrow a book
    public void borrowBook(Book book) {
        borrowedBooks.add(book);
    }

    // Return a book
    public void returnBook(Book book) {
        borrowedBooks.remove(book);
    }
}

/**
 * Main Library Management System class with menu-driven UI.
 */
public class LibraryManagementSystem {
    private static ArrayList<Book> books = new ArrayList<Book>();
    private static ArrayList<Borrower> borrowers = new ArrayList<Borrower>();
    private static Scanner sc = new Scanner(System.in);

    public static void main(String[] args) {
        while (true) {
            System.out.println("\n===== Library Management System =====");
            System.out.println("1. Add Book");
            System.out.println("2. View Books");
            System.out.println("3. Borrow Book");
            System.out.println("4. Return Book");
            System.out.println("5. Search Book");
            System.out.println("6. Exit");
            System.out.print("Choose an option: ");

            // Handle invalid input gracefully
            if (!sc.hasNextInt()) {
                System.out.println("Invalid input. Please enter a number.");
                sc.nextLine(); // clear buffer
                continue;
            }

            int choice = sc.nextInt();
            sc.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    addBook();
                    break;
                case 2:
                    viewBooks();
                    break;
                case 3:
                    borrowBook();
                    break;
                case 4:
                    returnBook();
                    break;
                case 5:
                    searchBook();
                    break;
                case 6:
                    System.out.println("Exiting system. Goodbye!");
                    return;
                default:
                    System.out.println("Invalid choice, try again.");
            }
        }
    }

    // Add a new book to the library
    private static void addBook() {
        System.out.print("Enter Book ID: ");
        int id = sc.nextInt();
        sc.nextLine();
        System.out.print("Enter Title: ");
        String title = sc.nextLine();
        System.out.print("Enter Author: ");
        String author = sc.nextLine();

        books.add(new Book(id, title, author));
        System.out.println("Book added successfully!");
    }

    // Display all books with availability
    private static void viewBooks() {
        if (books.isEmpty()) {
            System.out.println("No books available.");
            return;
        }
        for (Book b : books) {
            System.out.println(b);
        }
    }

    // Borrow a book if available
    private static void borrowBook() {
        System.out.print("Enter Borrower ID: ");
        int borrowerID = sc.nextInt();
        sc.nextLine();
        System.out.print("Enter Borrower Name: ");
        String borrowerName = sc.nextLine();

        Borrower borrower = findBorrower(borrowerID);
        if (borrower == null) {
            borrower = new Borrower(borrowerID, borrowerName);
            borrowers.add(borrower);
        }

        System.out.print("Enter Book ID to borrow: ");
        int bookID = sc.nextInt();

        Book book = findBook(bookID);
        if (book != null && book.isAvailable()) {
            book.setAvailable(false);
            borrower.borrowBook(book);
            System.out.println("Book borrowed successfully by " + borrower.getName());
        } else {
            System.out.println("Book not available.");
        }
    }

    // Return a borrowed book
    private static void returnBook() {
        System.out.print("Enter Borrower ID: ");
        int borrowerID = sc.nextInt();

        Borrower borrower = findBorrower(borrowerID);
        if (borrower == null) {
            System.out.println("Borrower not found.");
            return;
        }

        System.out.print("Enter Book ID to return: ");
        int bookID = sc.nextInt();

        Book book = findBook(bookID);
        if (book != null && borrower.getBorrowedBooks().contains(book)) {
            book.setAvailable(true);
            borrower.returnBook(book);
            System.out.println("Book returned successfully!");
        } else {
            System.out.println("Invalid return attempt.");
        }
    }

    // Search for books by title or author
    private static void searchBook() {
        System.out.print("Enter title or author keyword: ");
        String keyword = sc.nextLine().toLowerCase();

        boolean found = false;
        for (Book b : books) {
            if (b.getTitle().toLowerCase().contains(keyword) ||
                b.getAuthor().toLowerCase().contains(keyword)) {
                System.out.println(b);
                found = true;
            }
        }
        if (!found) {
            System.out.println("No books found.");
        }
    }

    // Helper: find book by ID
    private static Book findBook(int id) {
        for (Book b : books) {
            if (b.getBookID() == id) return b;
        }
        return null;
    }

    // Helper: find borrower by ID
    private static Borrower findBorrower(int id) {
        for (Borrower br : borrowers) {
            if (br.getBorrowerID() == id) return br;
        }
        return null;
    }
}

