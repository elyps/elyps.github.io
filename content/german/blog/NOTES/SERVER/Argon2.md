Below are two common approaches to "using" Argon2 on a Debian 12 server—either as a standalone command-line tool or as a library for integration into your C/C++ projects.

---

### 1. Installing and Using the Argon2 Command‐Line Utility

#### **Installation**

1. **Update package lists:**
    
    ```bash
    sudo apt update
    ```
    
2. **Install the Argon2 utility:**
    
    ```bash
    sudo apt install argon2
    ```
    
3. **Verify the installation:**
    
    ```bash
    argon2 --help
    ```
    
    This command displays the usage and available options.
    

#### **Usage Example**

The Argon2 command-line tool expects a salt string as its first argument followed by options that control the variant and parameters. For example, to use the Argon2id variant with 2 iterations, 64 MB memory (specified in KiB), and 4 parallel threads:

argon2 mySalt -id -t 2 -k 65536 -p 4

- **`mySalt`**: Replace with a salt value (for production, use a random string).
- **`-id`**: Chooses the Argon2id variant (use `-i` for Argon2i or `-d` for Argon2d).
- **`-t 2`**: Sets the number of iterations (time cost).
- **`-k 65536`**: Specifies memory cost in KiB (here 65,536 KiB ≈ 64 MB).
- **`-p 4`**: Uses 4 parallel threads.

Adjust these parameters based on your security requirements.

---

### 2. Installing and Using the Argon2 Library in a C Project

If you need to integrate Argon2 hashing directly into your C/C++ code, install the development package and link against the library.

#### **Installation**

sudo apt update
sudo apt install libargon2-1-dev

#### **Sample C Code**

Below is a simple C example that hashes a password using Argon2i:

#include <stdio.h>
#include <string.h>
#include <argon2.h>

int main(void) {
    // Input values
    const char *password = "mysecretpassword";
    const char *salt = "somesalt"; // In production, use a cryptographically secure random salt.

    // Buffer for the encoded hash output.
    // The recommended length is at least 108 characters for Argon2.
    char encoded[128];
    
    // Hash parameters: iterations, memory (in KiB), parallelism.
    uint32_t t_cost = 2;       // Number of iterations.
    uint32_t m_cost = 1 << 16; // Memory cost: 65,536 KiB (~64 MB).
    uint32_t parallelism = 4;  // Number of threads.

    // Generate the hash and encoded string.
    int result = argon2_hash(t_cost, m_cost, parallelism,
                             password, strlen(password),
                             salt, strlen(salt),
                             NULL, 0,          // Raw hash output (not used here).
                             encoded, sizeof(encoded),
                             Argon2_i,         // Use Argon2i variant.
                             ARGON2_VERSION_13 // Use version 1.3.
    );

    if (result != ARGON2_OK) {
        fprintf(stderr, "Error: %s\n", argon2_error_message(result));
        return 1;
    }

    printf("Encoded hash: %s\n", encoded);
    return 0;
}

#### **Compilation**

Compile the program by linking with the Argon2 library:

gcc -o argon2_example argon2_example.c -largon2

Then run the executable:

./argon2_example

This example demonstrates how to set up parameters and generate an encoded Argon2 hash.

---

Both approaches let you leverage Argon2 for secure password hashing on Debian 12. Adjust parameters and practices as needed for your production environment.
