# Python_project
import pandas as pd
import random

# Sample train data
train_data = {
    'Train': ['Shatabdi Express', 'Rajdhani Express', 'Garib Rath', 'Duronto Express'],
    'Sleeper Seats': [108, 150, 200, 120],
    'AC Seats': [50, 75, 60, 40],
    'Sleeper Price': [500, 700, 300, 600],
    'AC Price': [1200, 1500, 800, 1000]
}

trains = pd.DataFrame(train_data)

# DataFrame to store booking details
bookings = pd.DataFrame(columns=['PNR', 'Train', 'Class', 'Seats', 'Total Price'])

# Function to check seat availability
def check_availability(train, class_type, num_seats):
    if train in trains['Train'].values:
        index = trains[trains['Train'] == train].index[0]

        if class_type == 'Sleeper':
            return trains.loc[index, 'Sleeper Seats'] >= num_seats
        elif class_type == 'AC':
            return trains.loc[index, 'AC Seats'] >= num_seats
        else:
            print("❌ Invalid class type.")
            return False
    else:
        print("❌ Train not found.")
        return False

# Function to book tickets
def book_tickets(train, class_type, num_seats):
    global bookings  # Needed to modify the bookings DataFrame

    if check_availability(train, class_type, num_seats):
        index = trains[trains['Train'] == train].index[0]

        if class_type == 'Sleeper':
            trains.loc[index, 'Sleeper Seats'] -= num_seats
            price = trains.loc[index, 'Sleeper Price']
        elif class_type == 'AC':
            trains.loc[index, 'AC Seats'] -= num_seats
            price = trains.loc[index, 'AC Price']

        # Generate PNR and store booking
        pnr = random.randint(100000, 999999)
        total_price = price * num_seats
        new_booking = pd.DataFrame([[pnr, train, class_type, num_seats, total_price]], 
                                   columns=bookings.columns)
        bookings = pd.concat([bookings, new_booking], ignore_index=True)

        print(f"✅ Ticket booked successfully! PNR: {pnr}")
        print(f"Train: {train} | Class: {class_type} | Seats: {num_seats} | Total Price: ₹{total_price}")
    else:
        print(f"❌ Booking failed. Not enough seats in {train} ({class_type}).")

# Function to cancel booking
def cancel_booking(pnr):
    global bookings  # Needed to modify the bookings DataFrame

    if pnr in bookings['PNR'].values:
        booking = bookings[bookings['PNR'] == pnr].iloc[0]
        train, class_type, seats = booking['Train'], booking['Class'], booking['Seats']
        index = trains[trains['Train'] == train].index[0]

        # Restore seats
        trains.loc[index, f'{class_type} Seats'] += seats

        # Remove booking from history
        bookings = bookings[bookings['PNR'] != pnr]

        print(f"✅ Booking with PNR {pnr} has been canceled. {seats} seats restored.")
    else:
        print("❌ PNR not found.")

# Example Usage
book_tickets('Shatabdi Express', 'AC', 2)
book_tickets('Rajdhani Express', 'Sleeper', 5)
print("\n📊 Updated Train Availability:\n", trains)
print("\n📝 Booking History:\n", bookings)

# Cancel a booking
cancel_booking(bookings['PNR'].iloc[0])
print("\n📊 Updated Train Availability After Cancellation:\n", trains)
print("\n📝 Updated Booking History:\n", bookings)
