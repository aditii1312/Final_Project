# Final_Project
import json
import random

class User:
    def __init__(self, name, email, password):
        self.name = name
        self.email = email
        self.password = password

    def view_menu(self, menu):
        print("Menu:")
        for item_id, item_data in menu.items():
            print(f"ID: {item_id}")
            for key, value in item_data.items():
                print(f"{key}: {value}")
            print("-----------------------")

class Admin(User):
    def __init__(self, name, email, password):
        super().__init__(name, email, password)

    def register(self):
        # Register admin logic here
        admin_data = {
            "name": self.name,
            "email": self.email,
            "password": self.password
        }
        with open("admin.json", "w") as file:
            json.dump(admin_data, file)
        print("Admin registered successfully!")

    def login(self):
        # Admin login logic here
        with open("admin.json", "r") as file:
            admin_data = json.load(file)
            if admin_data["email"] == self.email and admin_data["password"] == self.password:
                print("Admin login successful!")
                return True
            else:
                print("Invalid credentials!")
                return False

    def add_food_item(self, menu):
        # Add food item to the menu
        item_name = input("Enter the name of the food item: ")
        item_price = float(input("Enter the price of the food item: "))
        quantity_per_plate = int(input("Enter the number of pieces per plate: "))
        item_discount = float(input("Enter the discount for the food item (%): "))
        item_stock = int(input("Enter the stock quantity of the food item: "))

        # Check for duplicate food items
        for food_id, food_data in menu.items():
            if food_data["name"] == item_name:
                print("Food item already exists!")
                return

        food_id = str(random.randint(1, 101))
        menu[food_id] = {
            "name": item_name,
            "price": item_price,
            "quantity": quantity_per_plate,
            "discount": item_discount,
            "stock": item_stock
        }

        with open("menu.json", "w") as file:
            json.dump(menu, file)
        print("Food item added successfully!")

    def update_food_item(self, menu):
        # Update food item in the menu
        food_id = input("Enter the ID of the food item to update: ")
        if food_id in menu:
            item_name = input("Enter the updated name of the food item: ")
            item_price = float(input("Enter the updated price of the food item: "))
            quantity_per_plate = int(input("Enter the updated quantity per plate: "))
            item_discount = float(input("Enter the updated discount for the food item (%): "))
            item_stock = int(input("Enter the updated stock quantity of the food item: "))

            # Check for duplicate food items
            for food_id, food_data in menu.items():
                if food_data["name"] == item_name and food_id != food_id:
                    print("Food item already exists!")
                    return

            menu[food_id] = {
                "name": item_name,
                "price": item_price,
                "quantity": quantity_per_plate,
                "discount": item_discount,
                "stock": item_stock
            }
            with open("menu.json", "w") as file:
                json.dump(menu, file)
            print("Food item updated successfully!")
        else:
            print("Food item not found!")

    def delete_food_item(self, menu):
        # Delete food item from the menu
        food_id = input("Enter the ID of the food item to delete: ")
        if food_id in menu:
            del menu[food_id]
            with open("menu.json", "w") as file:
                json.dump(menu, file)
            print("Food item deleted successfully!")
        else:
            print("Food item not found!")

class Customer(User):
    def __init__(self, name, email, password, phone_number, address):
        super().__init__(name, email, password)
        self.phone_number = phone_number
        self.address = address

    def register(self):
        customer_data = {
            "name": self.name,
            "email": self.email,
            "password": self.password,
            "phone_number": self.phone_number,
            "address": self.address,
            "order_history": []
        }

        with open("customer.json", "w") as file:
            json.dump(customer_data, file)

        print("Registration successful!")

    def login(self):
        with open("customer.json", "r") as file:
            customer_data = json.load(file)
            if (
                self.email == customer_data["email"]
                and self.password == customer_data["password"]
            ):
                print("Login successful!")
                return True
            else:
                print("Invalid email or password!")
                return False

    def place_order(self, menu, order_history):
        order_items = []
        total_amount = 0
        discount = 0

        while True:
            item_id = input("Enter the ID of the food item to order (or 'done' to finish): ")
            if item_id == "done":
                break

            if item_id in menu:
                quantity = int(input("Enter the quantity to order: "))
                if quantity <= menu[item_id]["stock"]:
                    item_price = menu[item_id]["price"]
                    item_discount = menu[item_id]["discount"]
                    item_total = item_price * quantity
                    item_total_discounted = item_total - (item_total * (item_discount / 100))
                    order_items.append({
                        "item_id": item_id,
                        "name": menu[item_id]["name"],
                        "quantity": quantity,
                        "price": item_price,
                        "discount": item_discount,
                        "total": item_total_discounted
                    })
                    menu[item_id]["stock"] -= quantity
                    total_amount += item_total_discounted
                    discount += item_total - item_total_discounted
                    print("Item added to the order!")
                else:
                    print("Insufficient stock!")
            else:
                print("Invalid item ID!")

        order = {
            "order_id": str(random.randint(1, 1001)),
            "order_items": order_items,
            "total_amount": total_amount,
            "discount": discount
        }

        order_history.append(order)

        with open("menu.json", "w") as file:
            json.dump(menu, file)

        print("Order placed successfully!")

    def view_order_history(self, order_history):
        if len(order_history) == 0:
            print("No order history found.")
        else:
            print("Order History:")
            for order in order_history:
                print("Order ID:", order["order_id"])
                print("Order Items:")
                for item in order["order_items"]:
                    print("  - Item ID:", item["item_id"])
                    print("    Name:", item["name"])
                    print("    Quantity:", item["quantity"])
                    print("    Price:", item["price"])
                    print("    Discount:", item["discount"])
                    print("    Total:", item["total"])
                print("Total Amount:", order["total_amount"])
                print("Discount:", order["discount"])
                print("-----------------------")

    def update_profile(self):
        with open("customer.json", "r") as file:
            customer_data = json.load(file)

        print("Update Profile:")
        customer_data["name"] = input("Enter your updated name: ")
        customer_data["email"] = input("Enter your updated email: ")
        customer_data["password"] = input("Enter your updated password: ")
        customer_data["phone_number"] = input("Enter your updated phone number: ")
        customer_data["address"] = input("Enter your updated address: ")

        with open("customer.json", "w") as file:
            json.dump(customer_data, file)

        print("Profile updated successfully!")

# Main program
menu = {}
order_history = []

while True:
    print("1. Admin")
    print("2. Customer")
    print("3. Exit")
    choice = input("Enter your choice: ")

    if choice == "1":
        admin_email = input("Enter your email: ")
        admin_password = input("Enter your password: ")
        admin = Admin("", admin_email, admin_password)
        if admin.login():
            while True:
                print("\nAdmin Menu:")
                print("1. View Food Menu")
                print("2. Add Food Item")
                print("3. Update Food Item")
                print("4. Delete Food Item")
                print("5. Logout")

                admin_choice = input("Enter your choice: ")

                if admin_choice == "1":
                    admin.view_menu(menu)
                elif admin_choice == "2":
                    admin.add_food_item(menu)
                elif admin_choice == "3":
                    admin.update_food_item(menu)
                elif admin_choice == "4":
                    admin.delete_food_item(menu)
                elif admin_choice == "5":
                    print("Admin logged out successfully!")
                    break
                else:
                    print("Invalid choice!")

    elif choice == "2":
        customer_email = input("Enter your email: ")
        customer_password = input("Enter your password: ")
        customer = Customer("", customer_email, customer_password, "", "")
        if customer.login():
            while True:
                print("\nCustomer Menu:")
                print("1. View Food Menu")
                print("2. Place Order")
                print("3. View Order History")
                print("4. Update Profile")
                print("5. Logout")

                customer_choice = input("Enter your choice: ")

                if customer_choice == "1":
                    customer.view_menu(menu)
                elif customer_choice == "2":
                    customer.place_order(menu, order_history)
                elif customer_choice == "3":
                    customer.view_order_history(order_history)
                elif customer_choice == "4":
                    customer.update_profile()
                elif customer_choice == "5":
                    print("Customer logged out successfully!")
                    break
                else:
                    print("Invalid choice!")


    elif choice == "3":
        print("Exiting program...")
        break

    else:
        print("Invalid choice!")
