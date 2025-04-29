package com.mycompany.airlinebookingsystem;

import java.util.*;

public class AirlineBookingSystem {
    static List<Flight> flights = new ArrayList<>();
    static List<Booking> bookings = new ArrayList<>();
    static Scanner scanner = new Scanner(System.in);
    static String companyName = "Moon's MilkyWay";

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
                case 1 -> viewFlights();
                case 2 -> bookFlight();
                case 3 -> viewBookings();
                case 4 -> {
                    System.out.println("Thanks for using " + companyName + ".");
                    return;
                }
                default -> System.out.println("Invalid option. Try again.");
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

            String mealPreference = switch (mealChoice) {
                case 1 -> "Vegetarian";
                case 2 -> "Non-Vegetarian";
                case 3 -> "Vegan";
                default -> {
                    System.out.println("Invalid choice, defaulting to Vegetarian.");
                    yield "Vegetarian";
                }
            };

            double mealCharge = switch (mealPreference) {
                case "Vegetarian" -> 10.0;
                case "Non-Vegetarian" -> 15.0;
                case "Vegan" -> 12.0;
                default -> 0.0;
            };

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

class Flight {
    int flightNumber;
    String source, destination;
    int availableSeats;
    double ticketPrice;

    public Flight(int flightNumber, String source, String destination, int availableSeats, double ticketPrice) {
        this.flightNumber = flightNumber;
        this.source = source;
        this.destination = destination;
        this.availableSeats = availableSeats;
        this.ticketPrice = ticketPrice;
    }

    public boolean bookSeat() {
        if (availableSeats > 0) {
            availableSeats--;
            return true;
        }
        return false;
    }

    public void displayFlight() {
        System.out.println("Flight " + flightNumber + ": " + source + " to " + destination + 
                           ", Seats: " + availableSeats + ", Price: $" + ticketPrice);
    }
}

class Booking {
    String passengerName;
    int flightNumber;
    String mealPreference;
    double totalCost;

    public Booking(String passengerName, int flightNumber, String mealPreference, double totalCost) {
        this.passengerName = passengerName;
        this.flightNumber = flightNumber;
        this.mealPreference = mealPreference;
        this.totalCost = totalCost;
    }

    public void displayBooking() {
        System.out.println("Passenger: " + passengerName + ", Flight: " + flightNumber + 
                           ", Meal: " + mealPreference + ", Total: $" + totalCost);
    }
}

