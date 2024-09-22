from cryptography.fernet import Fernet
import os

def generate_key():
    key = Fernet.generate_key()
    with open("secret.key", "wb") as key_file:
        key_file.write(key)

def load_key():
    return open("secret.key", "rb").read()

if not os.path.exists("secret.key"):
    generate_key()

key = load_key()
cipher = Fernet(key)
def encrypt_password(password):
    return cipher.encrypt(password.encode())

def decrypt_password(encrypted_password):
    return cipher.decrypt(encrypted_password).decode()
import json

def save_password(service, username, password):
    encrypted_password = encrypt_password(password)
    data = {service: {"username": username, "password": encrypted_password.decode()}}
    with open("passwords.json", "a") as file:
        json.dump(data, file)
        file.write("\n")

def load_passwords():
    passwords = {}
    if os.path.exists("passwords.json"):
        with open("passwords.json", "r") as file:
            for line in file:
                data = json.loads(line.strip())
                passwords.update(data)
    return passwords

def get_password(service):
    passwords = load_passwords()
    if service in passwords:
        encrypted_password = passwords[service]["password"]
        return decrypt_password(encrypted_password)
    else:
        return None
def main():
    while True:
        choice = input("Choose an option: [1] Add Password [2] Get Password [3] Exit: ")
        if choice == "1":
            service = input("Enter the service name: ")
            username = input("Enter the username: ")
            password = input("Enter the password: ")
            save_password(service, username, password)
            print("Password saved successfully!")
        elif choice == "2":
            service = input("Enter the service name: ")
            password = get_password(service)
            if password:
                print(f"Password for {service}: {password}")
            else:
                print("Service not found!")
        elif choice == "3":
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()
    
