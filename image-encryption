import numpy as np
from PIL import Image
import os
import random

class ImageEncryptor:
    def __init__(self):
        self.key = None
        
    def generate_key(self, image_shape):
        """Generate encryption key based on image dimensions"""
        height, width, channels = image_shape
        # Create a key array with the same shape as the image
        key = np.random.randint(0, 256, size=image_shape, dtype=np.uint8)
        return key
    
    def load_image(self, image_path):
        """Load image and convert to numpy array"""
        try:
            img = Image.open(image_path)
            img_array = np.array(img)
            return img_array
        except Exception as e:
            print(f"Error loading image: {e}")
            return None
    
    def save_image(self, img_array, output_path):
        """Save numpy array as image"""
        try:
            img = Image.fromarray(img_array)
            img.save(output_path)
            print(f"Image saved to: {output_path}")
        except Exception as e:
            print(f"Error saving image: {e}")
    
    def encrypt_pixel_swap(self, img_array):
        """Encrypt by swapping pixels randomly"""
        encrypted = img_array.copy()
        height, width, channels = encrypted.shape
        
        # Generate random swap indices
        swap_indices = []
        for _ in range(height * width // 2):
            i1 = random.randint(0, height - 1)
            j1 = random.randint(0, width - 1)
            i2 = random.randint(0, height - 1)
            j2 = random.randint(0, width - 1)
            swap_indices.append(((i1, j1), (i2, j2)))
        
        # Store swap indices as key
        self.key = swap_indices
        
        # Perform swaps
        for (i1, j1), (i2, j2) in swap_indices:
            encrypted[i1, j1], encrypted[i2, j2] = encrypted[i2, j2].copy(), encrypted[i1, j1].copy()
        
        return encrypted
    
    def decrypt_pixel_swap(self, encrypted_array):
        """Decrypt by reversing pixel swaps"""
        if self.key is None:
            print("No key available for decryption")
            return None
        
        decrypted = encrypted_array.copy()
        
        # Reverse the swaps in opposite order
        for (i1, j1), (i2, j2) in reversed(self.key):
            decrypted[i1, j1], decrypted[i2, j2] = decrypted[i2, j2].copy(), decrypted[i1, j1].copy()
        
        return decrypted
    
    def encrypt_mathematical(self, img_array, operation='add'):
        """Encrypt using mathematical operations on pixel values"""
        encrypted = img_array.copy()
        
        # Generate random value for operation
        if operation == 'add':
            value = np.random.randint(1, 100)
            self.key = ('add', value)
            encrypted = (encrypted.astype(int) + value) % 256
        elif operation == 'multiply':
            value = np.random.randint(2, 10)
            self.key = ('multiply', value)
            encrypted = (encrypted.astype(int) * value) % 256
        elif operation == 'xor':
            value = np.random.randint(1, 256)
            self.key = ('xor', value)
            encrypted = np.bitwise_xor(encrypted, value)
        
        return encrypted.astype(np.uint8)
    
    def decrypt_mathematical(self, encrypted_array):
        """Decrypt by reversing mathematical operations"""
        if self.key is None:
            print("No key available for decryption")
            return None
        
        decrypted = encrypted_array.copy()
        operation, value = self.key
        
        if operation == 'add':
            decrypted = (decrypted.astype(int) - value) % 256
        elif operation == 'multiply':
            # Find modular multiplicative inverse
            inverse = self.modular_inverse(value, 256)
            decrypted = (decrypted.astype(int) * inverse) % 256
        elif operation == 'xor':
            decrypted = np.bitwise_xor(decrypted, value)
        
        return decrypted.astype(np.uint8)
    
    def modular_inverse(self, a, m):
        """Find modular multiplicative inverse using extended Euclidean algorithm"""
        def extended_gcd(a, b):
            if a == 0:
                return b, 0, 1
            gcd, x1, y1 = extended_gcd(b % a, a)
            x = y1 - (b // a) * x1
            y = x1
            return gcd, x, y
        
        gcd, x, _ = extended_gcd(a, m)
        if gcd != 1:
            return None  # No inverse exists
        else:
            return (x % m + m) % m
    
    def encrypt_combined(self, img_array):
        """Combine both pixel swap and mathematical operations"""
        # First apply mathematical operation
        encrypted = self.encrypt_mathematical(img_array, 'xor')
        # Then apply pixel swap
        encrypted = self.encrypt_pixel_swap(encrypted)
        return encrypted
    
    def decrypt_combined(self, encrypted_array):
        """Decrypt combined encryption"""
        # First reverse pixel swap
        decrypted = self.decrypt_pixel_swap(encrypted_array)
        # Then reverse mathematical operation
        decrypted = self.decrypt_mathematical(decrypted)
        return decrypted

def main():
    encryptor = ImageEncryptor()
    
    print("=== Image Encryption Tool ===")
    print("1. Encrypt using Pixel Swapping")
    print("2. Encrypt using Mathematical Operations")
    print("3. Encrypt using Combined Method")
    print("4. Decrypt Image")
    print("5. Exit")
    
    while True:
        choice = input("\nEnter your choice (1-5): ")
        
        if choice == '1':
            image_path = input("Enter path to image: ")
            img_array = encryptor.load_image(image_path)
            if img_array is not None:
                encrypted = encryptor.encrypt_pixel_swap(img_array)
                output_path = input("Enter output path for encrypted image: ")
                encryptor.save_image(encrypted, output_path)
                print("Image encrypted successfully!")
        
        elif choice == '2':
            print("\nMathematical Operations:")
            print("1. Addition")
            print("2. Multiplication")
            print("3. XOR")
            op_choice = input("Choose operation (1-3): ")
            
            operations = {'1': 'add', '2': 'multiply', '3': 'xor'}
            operation = operations.get(op_choice, 'xor')
            
            image_path = input("Enter path to image: ")
            img_array = encryptor.load_image(image_path)
            if img_array is not None:
                encrypted = encryptor.encrypt_mathematical(img_array, operation)
                output_path = input("Enter output path for encrypted image: ")
                encryptor.save_image(encrypted, output_path)
                print("Image encrypted successfully!")
        
        elif choice == '3':
            image_path = input("Enter path to image: ")
            img_array = encryptor.load_image(image_path)
            if img_array is not None:
                encrypted = encryptor.encrypt_combined(img_array)
                output_path = input("Enter output path for encrypted image: ")
                encryptor.save_image(encrypted, output_path)
                print("Image encrypted successfully!")
        
        elif choice == '4':
            image_path = input("Enter path to encrypted image: ")
            encrypted_array = encryptor.load_image(image_path)
            if encrypted_array is not None:
                decrypted = encryptor.decrypt_combined(encrypted_array)
                if decrypted is not None:
                    output_path = input("Enter output path for decrypted image: ")
                    encryptor.save_image(decrypted, output_path)
                    print("Image decrypted successfully!")
        
        elif choice == '5':
            print("Exiting...")
            break
        
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()
