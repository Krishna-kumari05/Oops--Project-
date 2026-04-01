#include <iostream>
#include <vector>
#include <string>
#include <limits>
#include<fstream>
#include<iomanip>
#include <algorithm>
using namespace std;

//------------movie class----------
class Movie {
private:
    int id;
    string title;
    int availableSeats;
    double pricePerSeat;

public:
    Movie(int _id, string _title, int _seats, double _price)
        : id(_id), title(_title), availableSeats(_seats), pricePerSeat(_price) {}

    void display() const {
        cout << "ID: " << id
             << " | Title: " << title
             << " | Available Seats: " << availableSeats
             << " | Price per Seat: Rs" << pricePerSeat << endl;
    }

    // getter functions
    int getId() const { return id; }
    string getTitle() const { return title; }
    int getAvailableSeats() const { return availableSeats; }
    double getPricePerSeat() const { return pricePerSeat; }

    // update available seats after booking
    bool bookSeats(int seats) {
        if (seats > availableSeats) {
            return false;
        }
        availableSeats -= seats;
        return true;
    }

    void restoreSeats(int seats) {
        availableSeats += seats;
    }

};

//------------customer class ---------------
class Customer {
private:
    string name;
    string contactInfo; // can be phone number, email, etc.

public:
    Customer(string _name, string _contactInfo)
        : name(_name), contactInfo(_contactInfo) {}
    //
    virtual ~Customer() {}

    virtual double getDiscountRate() const {
        return 0.0;
    }
    virtual string getCustomerType() const {
        return "Regular";
    }
    //

    // getter functions (to avoid direct access to data)
    string getName() const { return name; }
    string getContactInfo() const { return contactInfo; }

    // setter functions (if required in the future)
    void setName(const string& newName){name=newName;}
    void setContactInfo(const string& newContactInfo){contactInfo=newContactInfo;}
};

class VIPCustomer : public Customer {
    public:
        VIPCustomer(string _name, string _contactInfo)
            : Customer(_name, _contactInfo) {}
    
        double getDiscountRate() const override {
            return 0.15;
        }
    
        string getCustomerType() const override {
            return "VIP";
        }
};
    

//------------theater class---------------
class Theater {
private:
    string theaterName;
    vector<Movie>movies;
    int nextMovieId;

public:
    Theater(string _name):theaterName(_name),nextMovieId(1) {}

    void addMovie(string title, int seats, double price) {
        movies.push_back(Movie(nextMovieId++, title, seats, price));
    }

    void displayMovies() const {
        cout << "\n---------- Available Movies in " << theaterName << " ----------" << endl;
        for (int i = 0; i < movies.size(); i++) {
            movies[i].display();
        }
        cout << "-----------------------------------------------------" << endl;
    }

    Movie* findMovieById(int id) {
        for (int i = 0; i < movies.size(); i++) {
            if (movies[i].getId() == id) {
                return &movies[i];
            }
        }
        return nullptr;
    }

    string getTheaterName() const { return theaterName; }
};

//------------movie theater class---------------
class MovieTheater {
private:
    vector<Theater> theaters;

public:
    void addTheater(string theaterName) {
        theaters.push_back(Theater(theaterName));
    }

    void displayTheaters() const {
        cout << "\n---------- Available Theaters ----------" << endl;
        for (size_t i = 0; i < theaters.size(); i++) {
            cout << i + 1 << ". " << theaters[i].getTheaterName() << endl;
        }
        cout << "----------------------------------------" << endl;
    }

    void displayMoviesInTheater(int theaterIndex) const {
        if (theaterIndex < 0 || theaterIndex >= theaters.size()) {
            cout << "Invalid theater selection." << endl;
            return;
        }
        theaters[theaterIndex].displayMovies();
    }

    Movie* findMovieByIdInTheater(int theaterIndex, int movieId) {
        if (theaterIndex < 0 || theaterIndex >= theaters.size()) {
            return nullptr;
        }
        return theaters[theaterIndex].findMovieById(movieId);
    }

    void addMovieToTheater(int theaterIndex, string title, int seats, double price) {
        if (theaterIndex < 0 || theaterIndex >= theaters.size()) {
            cout << "Invalid theater index." << endl;
            return;
        }
        theaters[theaterIndex].addMovie(title, seats, price);
    }

    size_t getTheaterCount() const {
        return theaters.size();
    }
};

//------------booking class---------------
class Booking {
private:
    int bookingId;
    Customer* customer; // Reference to Customer class
    Movie* movie;
    int seatsBooked;
    double totalPrice;

public:
    Booking(int _bookingId, Customer* _customer, Movie* _movie, int _seats)
        : bookingId(_bookingId), customer(_customer), movie(_movie), seatsBooked(_seats) {
    double discount = customer->getDiscountRate();
    totalPrice = seatsBooked * movie->getPricePerSeat() * (1 - discount);
    }

    void displayBooking() const {
        cout << "Booking ID: " << bookingId
             << " | Customer: " << customer->getName()
             << " | Movie: " << movie->getTitle()
             << " | Seats Booked: " << seatsBooked
             << " | Total Price: Rs " << totalPrice
             << endl;
    }

    int getBookingId() const { return bookingId; }
    Movie* getMovie() const { return movie; }
    int getSeatsBooked() const { return seatsBooked; }
    Customer* getCustomer() const { return customer; }
   
};

//------------booking manager class---------------
class BookingManager {
private:
    vector<Booking> bookings;
    int nextBookingId;


    void saveBookingsToFile() const {
        ofstream outFile("bookings.csv");
        outFile << "BookingID,CustomerName,Contact,MovieTitle,SeatsBooked,TotalPrice\n";
        for (const auto& b : bookings) {
            outFile << b.getBookingId() << ","
                    << b.getCustomer()->getName() << ","
                    << b.getCustomer()->getContactInfo() << ","
                    << b.getMovie()->getTitle() << ","
                    << b.getSeatsBooked() << ","
                    << fixed << setprecision(2)
                    << (b.getSeatsBooked() * b.getMovie()->getPricePerSeat()) << "\n";
        }
    }

public:
    BookingManager() : nextBookingId(1) {}

    bool addBooking(Customer* customer, Movie* movie, int seats) {
        if (movie->bookSeats(seats)) {
            Booking newBooking(nextBookingId++, customer, movie, seats);
            bookings.push_back(newBooking);
            cout << "\nBooking Successful!" << endl;
            newBooking.displayBooking();
            saveBookingsToFile();
            return true;
        } else {
            cout << "\nError: Not enough seats available for this movie." << endl;
            return false;
        }
    }

    void displayBookings() const {
        if (bookings.empty()) {
            cout << "\nNo bookings made yet." << endl;
            return;
        }
        cout << "\n---------- Booking History ----------" << endl;
        for (size_t i = 0; i < bookings.size(); i++) {
            bookings[i].displayBooking();
        }
        cout << "-----------------------------------------------------" << endl;
    }


    bool cancelBooking(int bookingId) {
        for (auto it = bookings.begin(); it != bookings.end(); ++it) {
            if (it->getBookingId() == bookingId) {
                double total = it->getSeatsBooked() * it->getMovie()->getPricePerSeat();
                double penalty = total * 0.10;
                double refund = total - penalty;

                it->getMovie()->restoreSeats(it->getSeatsBooked());
                cout << "Booking cancelled.\nRefund: Rs " << fixed << setprecision(2) << refund
                     << " (Penalty: Rs " << penalty << ")" << endl;
                bookings.erase(it);
                saveBookingsToFile();
                return true;
            }
        }
        cout << "Booking ID not found." << endl;
        return false;
    }
    


};

// check whether email is valid
bool isValidEmail(const string& email) {
    size_t atPos = email.find('@');
    size_t dotPos = email.find('.', atPos);

    if (atPos == string::npos || dotPos == string::npos) return false;
    if (atPos == 0 || atPos == email.length() - 1) return false;
    if (dotPos == email.length() - 1 || dotPos == atPos + 1) return false;
    if (email.find(' ') != string::npos) return false;

    return true;
}

// loop until a valid email is entered
string getValidatedEmail() {
    string email;
    while (true) {
        cout << "Enter your email: ";
        getline(cin, email);
        if (isValidEmail(email)) {
            return email;
        } else {
            cout << "gInvalid email! Example: user@example.com\n";
        }
    }
}

//check for valid length of mobile no.
bool isValidPhone(const string& phone) {
    return phone.length() == 10 && all_of(phone.begin(), phone.end(), ::isdigit);
}

// loop until a valid phone number is entered
string getValidatedPhone() {
    string phone;
    while (true) {
        cout << "Enter your phone number (10 digits): ";
        getline(cin, phone);
        if (isValidPhone(phone)) {
            return phone;
        } else {
            cout << "Invalid phone! Enter exactly 10 digits, only numbers.\n";
        }
    }
}

//------------ticket booking system class---------------
class TicketBookingSystem {
private:
    MovieTheater theater;
    BookingManager bookingManager;

    void clearInput() {
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
    }

    void processBooking() {
        int theaterChoice, movieId, seats;
        string customerName, contactInfo;
        char confirm,vipStatus;

        theater.displayTheaters();  // Display available theaters

        cout << "\nEnter the number of the theater you want to book from: ";
        cin >> theaterChoice;
        if(cin.fail() || theaterChoice < 1 || theaterChoice > theater.getTheaterCount()) {
            clearInput();
            cout << "Invalid theater choice. Please try again." << endl;
            return;
        }

        // Adjust theaterChoice to 0-based index
        theaterChoice -= 1;

        theater.displayMoviesInTheater(theaterChoice);  // Display movies for the selected theater

        cout << "\nEnter the ID of the movie you want to book: ";
        cin >> movieId;
        if(cin.fail()) {
            clearInput();
            cout << "Invalid movie ID. Please try again." << endl;
            return;
        }

        Movie* selectedMovie = theater.findMovieByIdInTheater(theaterChoice, movieId);
        if (selectedMovie == nullptr) {
            cout << "Movie with ID " << movieId << " not found." << endl;
            return;
        }

        cout << "Do you want to book this movie? (y/n): ";
        cin >> confirm;
        if (confirm != 'y' && confirm != 'Y') {
            cout << "Booking cancelled." << endl;
            return;
        }
        
        cout << "Are you a VIP customer? (y/n): ";
        cin >> vipStatus;
        clearInput();

        //user details
        cout << "Enter your name: ";
        getline(cin, customerName);
        char type;
        cout << "Are you entering an email (e) or phone number (p)? ";
        cin >> type;
        cin.ignore(); // clear the newline after entering the character

        if (type == 'e' || type == 'E'){
            contactInfo = getValidatedEmail();}
        else if (type == 'p' || type == 'P') {
            contactInfo = getValidatedPhone();}
        else {
            cout << "Invalid option. Defaulting to phone number.\n";
            contactInfo = getValidatedPhone();}


        Customer* customer;
        if (vipStatus == 'y' || vipStatus == 'Y') {
            customer = new VIPCustomer(customerName, contactInfo);
        } else {
            customer = new Customer(customerName, contactInfo);
        }

        cout << "Enter number of seats to book: ";
        cin >> seats;
        if(cin.fail() || seats <= 0) {
            clearInput();
            cout << "Invalid number of seats. Please try again." << endl;
            return;
        }
        //

        // Process the booking with customer info
        bookingManager.addBooking(customer, selectedMovie, seats);
    }

        void cancelBooking() {
        int bookingId;
        cout << "Enter Booking ID to cancel: ";
        cin >> bookingId;
        bookingManager.cancelBooking(bookingId);
    }


    void displayMenu() {
        cout << "\n---------- Movie Ticket Booking System ----------" << endl;
        cout << "1. Show Available Theaters" << endl;
        cout << "2. Show Available Movies in a Theater" << endl;
        cout << "3. Book Tickets" << endl;
        cout << "4. View Booking History" << endl;
        cout << "5. Cancel Booking" << endl;
        cout << "6. Exit" << endl;
        cout << "-----------------------------------------------------" << endl;
        cout << "Enter your choice (1-6): ";
    }

public:
    TicketBookingSystem() {
        // Pre-load some theaters and movies into the system

        // Adding Theater 1
        theater.addTheater("One8 Cinemas");
        theater.addMovieToTheater(0, "Ms Dhoni:The untold story", 50, 350);
        theater.addMovieToTheater(0, "Pushpa2", 60, 300);

        // Adding Theater 2
        theater.addTheater("Hongkong Theater");
        theater.addMovieToTheater(1, "18 :The father's dream", 100, 500);
        theater.addMovieToTheater(1, "Jawan", 80, 450);

        // Adding Theater 3
        theater.addTheater("Gold Cinemas");
        theater.addMovieToTheater(2, "Shershah", 70, 400);
        theater.addMovieToTheater(2, "War2", 65, 380);
    }

    void run() {
        int choice;
        while (true) {

            displayMenu();
            cin >> choice;
            
        if (cin.fail()) {
            cin.clear();  // clear the fail flag
            cin.ignore(numeric_limits<streamsize>::max(), '\n');  // discard invalid input
            cout << "Invalid input! Please enter a number between 1 and 6.\n";
            continue;  // Re-display menu
        }
            switch (choice) {
                case 1:
                    theater.displayTheaters();
                    break;
                case 2: {
                    int theaterChoice;
                    cout << "Enter the number of the theater: ";
                    cin >> theaterChoice;
                    theater.displayMoviesInTheater(theaterChoice - 1);  // Convert to 0-based index
                    break;
                }
                case 3:
                    processBooking();
                    break;
                case 4:
                    bookingManager.displayBookings();
                    break;
                case 5:
                    cancelBooking();
                    break;
                case 6:
                    cout << "Thank you for using the Ticket Booking System!" << endl;
                    return;
                default:
                    cout << "Invalid option. Please try again." << endl;
            }
        }
    }
};

int main() {
    TicketBookingSystem system;
    system.run();
    return 0;
}
