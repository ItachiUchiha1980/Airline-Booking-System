package com.mycompany.airlinebookingsystem; 

import java.util.*; 

class Flight { int flightNumber; String origin; String destination; int seatsAvailable; double ticketPrice; 

public Flight(int flightNumber, String origin, String destination, int seatsAvailable, double ticketPrice) { 
    this.flightNumber = flightNumber; 
    this.origin = origin; 
    this.destination = destination; 
    this.seatsAvailable = seatsAvailable; 
    this.ticketPrice = ticketPrice; 
} 
 
public boolean bookSeat() { 
    if (seatsAvailable > 0) { 
        seatsAvailable--; 
        return true; 
    } 
    return false; 
} 
 
public void displayFlight() { 
    System.out.println("Flight #" + flightNumber + " | From: " + origin + " -> To: " + destination + 
                       " | Seats Available: " + seatsAvailable + " | Price: $" + ticketPrice); 
} 
  

} 

class Booking { String passengerName; int flightNumber; String mealPreference; double totalCost; 

public Booking(String passengerName, int flightNumber, String mealPreference, double totalCost) { 
    this.passengerName = passengerName; 
    this.flightNumber = flightNumber; 
    this.mealPreference = mealPreference; 
    this.totalCost = totalCost; 
} 
 
public void displayBooking() { 
    System.out.println("Booking - Passenger: " + passengerName + " | Flight Number: " + flightNumber + 
                       " | Meal Preference: " + mealPreference + " | Total Cost: $" + totalCost); 
} 
  

} 

class Box { double width; double height; double depth; 

public Box(double w, double h, double d) { 
    width = w; 
    height = h; 
    depth = d;   
} 
 
 
public double calculateVolume() { 
    return width * height * depth; 
} 
 
 
public void displayBox() { 
    System.out.println("Box dimensions (W x H x D): " + width + " x " + height + " x " + depth); 
    System.out.println("Volume: " + calculateVolume()); 
} 
  

} 

public class AirlineBookingSystem { static List flights = new ArrayList<>(); static List bookings = new ArrayList<>(); static Scanner scanner = new Scanner(System.in); static String companyName = "Moon's MilkyWay"; 

public static void main(String[] args) { 
     
    flights.add(new Flight(101, "New York", "London", 5, 500.0)); 
    flights.add(new Flight(202, "Los Angeles", "Tokyo", 3, 700.0)); 
    flights.add(new Flight(303, "Paris", "Rome", 2, 300.0)); 
 
    while (true) { 
        System.out.println("\n--- " + companyName + " ---"); 
        System.out.println("1. View Flights"); 
        System.out.println("2. Book a Flight"); 
        System.out.println("3. View Bookings"); 
        System.out.println("4. Exit"); 
        System.out.print("Choose an option: "); 
        int choice = scanner.nextInt(); 
        scanner.nextLine();  
 
        switch (choice) { 
            case 1: 
                viewFlights(); 
                break; 
            case 2: 
                bookFlight(); 
                break; 
            case 3: 
                viewBookings(); 
                break; 
            case 4: 
                System.out.println("Thanks for using " + companyName + "."); 
                return; 
            default: 
                System.out.println("Invalid option. Try again."); 
        } 
    } 
} 
 
static void viewFlights() { 
    System.out.println("\nAvailable Flights:"); 
    for (Flight flight : flights) { 
        flight.displayFlight(); 
    } 
} 
 
static void bookFlight() { 
    System.out.print("Enter your name: "); 
    String name = scanner.nextLine(); 
    viewFlights(); 
    System.out.print("Enter the flight number to book: "); 
    int flightNumber = scanner.nextInt(); 
    scanner.nextLine();  
 
     
    Flight selectedFlight = null; 
    for (Flight flight : flights) { 
        if (flight.flightNumber == flightNumber) { 
            selectedFlight = flight; 
            break; 
        } 
    } 
 
    if (selectedFlight != null && selectedFlight.bookSeat()) { 
         
        System.out.println("\nMeal Preferences:"); 
        System.out.println("1. Vegetarian"); 
        System.out.println("2. Non-Vegetarian"); 
        System.out.println("3. Vegan"); 
        System.out.print("Choose your meal preference: "); 
        int mealChoice = scanner.nextInt(); 
        scanner.nextLine();  
        String mealPreference = ""; 
        switch (mealChoice) { 
            case 1: 
                mealPreference = "Vegetarian"; 
                break; 
            case 2: 
                mealPreference = "Non-Vegetarian"; 
                break; 
            case 3: 
                mealPreference = "Vegan"; 
                break; 
            default: 
                System.out.println("Invalid choice, defaulting to Vegetarian."); 
                mealPreference = "Vegetarian"; 
        } 
 
      
        double mealCharge = 0.0; 
        if (mealPreference.equals("Vegetarian")) { 
            mealCharge = 10.0;  
        } else if (mealPreference.equals("Non-Vegetarian")) { 
            mealCharge = 15.0;  
        } else if (mealPreference.equals("Vegan")) { 
            mealCharge = 12.0;  
        } 
 
        double totalCost = selectedFlight.ticketPrice + mealCharge; 
 
        
        bookings.add(new Booking(name, flightNumber, mealPreference, totalCost)); 
        System.out.println("Booking successful! Total cost: $" + totalCost); 
    } else { 
        System.out.println("Booking failed! Flight not found or no seats available."); 
    } 
} 
 
static void viewBookings() { 
    if (bookings.isEmpty()) { 
        System.out.println("No bookings found."); 
    } else { 
        System.out.println("\nYour Bookings:"); 
        for (Booking booking : bookings) { 
            booking.displayBooking(); 
        } 
    } 
} 
  

}
