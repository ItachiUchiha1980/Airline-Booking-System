package com.mycompany.airlinebookingsystem;

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.List;

public class AirlineBookingSystem extends JFrame {
    private List<Flight> flights = new ArrayList<>();
    private List<Booking> bookings = new ArrayList<>();
    private String companyName = "Air Deccan";

    // UI Components
    private JPanel mainPanel;
    private CardLayout cardLayout;
    private JPanel flightsPanel, bookingPanel, viewBookingsPanel;

    public AirlineBookingSystem() {
        // Initialize flights
        flights.add(new Flight(175, "London", "New York", 5, 1000.0));
        flights.add(new Flight(727, "India", "Switzerland", 3, 5000.0));
        flights.add(new Flight(609, "Paris", "Rome", 2, 1000.0));

        // Set up main window
        setTitle(companyName + " Booking System");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setSize(600, 500);
        setLayout(new BorderLayout());

        // Create menu panel
        JPanel menuPanel = new JPanel(new FlowLayout());
        JButton viewFlightsBtn = new JButton("View Flights");
        JButton bookFlightBtn = new JButton("Book a Flight");
        JButton viewBookingsBtn = new JButton("View Bookings");

        viewFlightsBtn.addActionListener(e -> showPanel("FLIGHTS"));
        bookFlightBtn.addActionListener(e -> showPanel("BOOKING"));
        viewBookingsBtn.addActionListener(e -> showPanel("BOOKINGS"));

        menuPanel.add(viewFlightsBtn);
        menuPanel.add(bookFlightBtn);
        menuPanel.add(viewBookingsBtn);
        add(menuPanel, BorderLayout.NORTH);

        // Create main panel with CardLayout
        cardLayout = new CardLayout();
        mainPanel = new JPanel(cardLayout);

        // Create and add panels
        flightsPanel = createFlightsPanel();
        bookingPanel = createBookingPanel();
        viewBookingsPanel = createViewBookingsPanel();

        mainPanel.add(flightsPanel, "FLIGHTS");
        mainPanel.add(bookingPanel, "BOOKING");
        mainPanel.add(viewBookingsPanel, "BOOKINGS");

        add(mainPanel, BorderLayout.CENTER);

        // Show initial panel
        showPanel("FLIGHTS");
    }

    private void showPanel(String panelName) {
        cardLayout.show(mainPanel, panelName);
        // Refresh panels when shown
        if (panelName.equals("FLIGHTS")) {
            mainPanel.remove(flightsPanel);
            flightsPanel = createFlightsPanel();
            mainPanel.add(flightsPanel, "FLIGHTS");
        } else if (panelName.equals("BOOKINGS")) {
            mainPanel.remove(viewBookingsPanel);
            viewBookingsPanel = createViewBookingsPanel();
            mainPanel.add(viewBookingsPanel, "BOOKINGS");
        }
    }

    private JPanel createFlightsPanel() {
        JPanel panel = new JPanel(new BorderLayout());
        JTextArea flightsText = new JTextArea(15, 50);
        flightsText.setEditable(false);

        StringBuilder sb = new StringBuilder("Available Flights:\n");
        for (Flight flight : flights) {
            sb.append("Flight ").append(flight.flightNumber)
                    .append(": ").append(flight.source)
                    .append(" to ").append(flight.destination)
                    .append(", Seats: ").append(flight.availableSeats)
                    .append(", Price: $").append(flight.ticketPrice)
                    .append("\n");
        }

        flightsText.setText(sb.toString());
        panel.add(new JScrollPane(flightsText), BorderLayout.CENTER);
        return panel;
    }

    private JPanel createBookingPanel() {
        JPanel panel = new JPanel();
        panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));

        // Passenger name
        JPanel namePanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        namePanel.add(new JLabel("Passenger Name:"));
        JTextField nameField = new JTextField(20);
        namePanel.add(nameField);
        panel.add(namePanel);

        // Flight selection
        JPanel flightPanel = new JPanel(new BorderLayout());
        flightPanel.add(new JLabel("Select Flight:"), BorderLayout.NORTH);

        JList<String> flightList = new JList<>(getFlightListData());
        flightList.setSelectionMode(ListSelectionModel.SINGLE_SELECTION);
        flightPanel.add(new JScrollPane(flightList), BorderLayout.CENTER);
        panel.add(flightPanel);

        // Meal preference
        JPanel mealPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        mealPanel.add(new JLabel("Meal Preference:"));
        ButtonGroup mealGroup = new ButtonGroup();
        JRadioButton veg = new JRadioButton("Vegetarian (+$10)", true);
        JRadioButton nonVeg = new JRadioButton("Non-Vegetarian (+$15)");
        JRadioButton vegan = new JRadioButton("Vegan (+$12)");
        mealGroup.add(veg);
        mealGroup.add(nonVeg);
        mealGroup.add(vegan);
        mealPanel.add(veg);
        mealPanel.add(nonVeg);
        mealPanel.add(vegan);
        panel.add(mealPanel);

        // Book button
        JPanel buttonPanel = new JPanel(new FlowLayout());
        JButton bookButton = new JButton("Book Flight");
        JTextArea resultArea = new JTextArea(3, 50);
        resultArea.setEditable(false);

        bookButton.addActionListener(e -> {
            String name = nameField.getText().trim();
            if (name.isEmpty()) {
                resultArea.setText("Please enter your name.");
                return;
            }

            int selectedIndex = flightList.getSelectedIndex();
            if (selectedIndex == -1) {
                resultArea.setText("Please select a flight.");
                return;
            }

            Flight selectedFlight = flights.get(selectedIndex);
            if (!selectedFlight.bookSeat()) {
                resultArea.setText("Booking failed! No seats available on this flight.");
                return;
            }

            String mealChoice;
            double mealCharge = 0.0;
            if (veg.isSelected()) {
                mealChoice = "Vegetarian";
                mealCharge = 10.0;
            } else if (nonVeg.isSelected()) {
                mealChoice = "Non-Vegetarian";
                mealCharge = 20.0;
            } else {
                mealChoice = "Both";
                mealCharge = 15.0;
            }

            double totalCost = selectedFlight.ticketPrice + mealCharge;

            bookings.add(new Booking(name, selectedFlight.flightNumber, mealChoice, totalCost));
            resultArea.setText("Booking successful!\n" +
                    "Passenger: " + name + "\n" +
                    "Flight: " + selectedFlight.flightNumber + "\n" +
                    "Total Cost: $" + totalCost);

            // Clear fields for next booking
            nameField.setText("");
            flightList.clearSelection();
            veg.setSelected(true);
        });

        buttonPanel.add(bookButton);
        panel.add(buttonPanel);
        panel.add(new JScrollPane(resultArea));

        return panel;
    }

    private String[] getFlightListData() {
        String[] flightData = new String[flights.size()];
        for (int i = 0; i < flights.size(); i++) {
            Flight flight = flights.get(i);
            flightData[i] = String.format("Flight %d: %s to %s ($%.2f, Seats: %d)",
                    flight.flightNumber, flight.source, flight.destination,
                    flight.ticketPrice, flight.availableSeats);
        }
        return flightData;
    }

    private JPanel createViewBookingsPanel() {
        JPanel panel = new JPanel(new BorderLayout());
        JTextArea bookingsText = new JTextArea(15, 50);
        bookingsText.setEditable(false);

        if (bookings.isEmpty()) {
            bookingsText.setText("No bookings found.");
        } else {
            StringBuilder sb = new StringBuilder("Your Bookings:\n");
            for (Booking booking : bookings) {
                sb.append("Passenger: ").append(booking.passengerName)
                        .append(", Flight: ").append(booking.flightNumber)
                        .append(", Meal: ").append(booking.mealPreference)
                        .append(", Total: $").append(booking.totalCost)
                        .append("\n");
            }
            bookingsText.setText(sb.toString());
        }

        panel.add(new JScrollPane(bookingsText), BorderLayout.CENTER);
        return panel;
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            AirlineBookingSystem system = new AirlineBookingSystem();
            system.setVisible(true);
        });
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
}

