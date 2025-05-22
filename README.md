# EX No. 11 : ELLIPTIC CURVE CRYPTOGRAPHY (ECC)

### Name : Prajin S
### Register Number : 212223230151

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

#### Step 01:
Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

#### Step 02: Initialization
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

##### Step 03: Key Generation
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

#### Step 04: Encryption and Decryption
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

#### Step 05: Security
ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```C
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    long long int x, y;
} Point;

// Function to compute modular inverse using Extended Euclidean Algorithm
long long int modInverse(long long int a, long long int m) {
    long long int m0 = m, t, q;
    long long int x0 = 0, x1 = 1;

    if (m == 1) return 0;

    while (a > 1) {
        q = a / m;
        t = m;

        m = a % m, a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
    }

    if (x1 < 0) x1 += m0;

    return x1;
}

// Function to perform point addition on the elliptic curve
Point pointAddition(Point P, Point Q, long long int a, long long int p) {
    Point R;
    long long int lambda;

    if (P.x == Q.x && P.y == Q.y) {
        long long int denominator = (2 * P.y) % p;
        if (denominator == 0) {
            R.x = R.y = 0; // Point at infinity
            return R;
        }
        lambda = (3 * P.x * P.x + a) * modInverse(denominator, p) % p;
    } else {
        long long int dx = (Q.x - P.x + p) % p;
        long long int dy = (Q.y - P.y + p) % p;
        if (dx == 0) {
            R.x = R.y = 0; // Point at infinity
            return R;
        }
        lambda = dy * modInverse(dx, p) % p;
    }

    R.x = (lambda * lambda - P.x - Q.x + p) % p;
    R.y = (lambda * (P.x - R.x) - P.y + p) % p;

    R.x = (R.x + p) % p;
    R.y = (R.y + p) % p;

    return R;
}

// Scalar multiplication using Double-and-Add method
Point scalarMultiplication(Point P, long long int k, long long int a, long long int p) {
    Point result = {0, 0}; // Identity (point at infinity)
    Point addend = P;

    while (k > 0) {
        if (k % 2 == 1) {
            if (result.x == 0 && result.y == 0) {
                result = addend;
            } else {
                result = pointAddition(result, addend, a, p);
            }
        }
        addend = pointAddition(addend, addend, a, p);
        k /= 2;
    }

    return result;
}

int main() {
    long long int p, a, b, privateA, privateB;
    Point G, publicA, publicB, sharedSecretA, sharedSecretB;

    printf("Enter the prime number p: ");
    scanf("%lld", &p);

    printf("Enter the elliptic curve parameters a and b (for y^2 = x^3 + ax + b): ");
    scanf("%lld %lld", &a, &b);

    printf("Enter the base point G (x and y): ");
    scanf("%lld %lld", &G.x, &G.y);

    printf("Enter Alice's private key: ");
    scanf("%lld", &privateA);

    printf("Enter Bob's private key: ");
    scanf("%lld", &privateB);

    publicA = scalarMultiplication(G, privateA, a, p);
    publicB = scalarMultiplication(G, privateB, a, p);

    printf("Alice's public key: (%lld, %lld)\n", publicA.x, publicA.y);
    printf("Bob's public key: (%lld, %lld)\n", publicB.x, publicB.y);

    sharedSecretA = scalarMultiplication(publicB, privateA, a, p);
    sharedSecretB = scalarMultiplication(publicA, privateB, a, p);

    printf("Shared secret computed by Alice: (%lld, %lld)\n", sharedSecretA.x, sharedSecretA.y);
    printf("Shared secret computed by Bob: (%lld, %lld)\n", sharedSecretB.x, sharedSecretB.y);

    if (sharedSecretA.x == sharedSecretB.x && sharedSecretA.y == sharedSecretB.y) {
        printf("Key exchange successful. Both shared secrets match!\n");
    } else {
        printf("Key exchange failed. Shared secrets do not match.\n");
    }

    return 0;
}
```


## Output:
Enter the prime number p: 17

Enter the elliptic curve parameters a and b (for y^2 = x^3 + ax + b): 2
3

Enter the base point G (x and y): 5
1

Enter Alice's private key: 7

Enter Bob's private key: 9

Alice's public key: (0, 6)

Bob's public key: (7, 6)

Shared secret computed by Alice: (16, 13)

Shared secret computed by Bob: (16, 13)

Key exchange successful. Both shared secrets match!

![Screenshot 2025-05-05 154341](https://github.com/user-attachments/assets/c0beb038-ee67-4de3-bfe1-008e4318cb25)


![Screenshot 2025-05-05 154312](https://github.com/user-attachments/assets/7ed17553-01c4-43cf-ae58-bff6aa67ff49)

## Result:
The program is executed successfully and results are verified as well.

