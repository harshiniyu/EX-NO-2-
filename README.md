## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
 

 

To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




## Program:

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define SIZE 100

// Convert string to lowercase
void toLowerCase(char str[], int len) {
   for (int i = 0; i < len; i++) {
       if (str[i] >= 'A' && str[i] <= 'Z')
           str[i] += 32;
   }
}

// Remove spaces
int removeSpaces(char str[], int len) {
   int count = 0;
   for (int i = 0; i < len; i++) {
       if (str[i] != ' ')
           str[count++] = str[i];
   }
   str[count] = '\0';
   return count;
}

// Generate 5x5 key table
void generateKeyTable(char key[], int ks, char keyT[5][5]) {
   int dict[26] = {0};
   int i, j, k;

   // Mark key chars
   for (i = 0; i < ks; i++) {
       if (key[i] != 'j')
           dict[key[i] - 'a'] = 2;
   }
   dict['j' - 'a'] = 1;  // treat i/j as same

   i = j = 0;

   // Fill table with key
   for (k = 0; k < ks; k++) {
       if (dict[key[k] - 'a'] == 2) {
           dict[key[k] - 'a'] = 1;
           keyT[i][j] = key[k];
           j++;
           if (j == 5) {
               i++;
               j = 0;
           }
       }
   }

   // Fill rest of alphabet
   for (k = 0; k < 26; k++) {
       if (dict[k] == 0) {
           keyT[i][j] = (char)(k + 'a');
           j++;
           if (j == 5) {
               i++;
               j = 0;
           }
       }
   }
}

// Print key table
void printKeyTable(char keyT[5][5]) {
   printf("\nKey Table:\n");
   for (int i = 0; i < 5; i++) {
       for (int j = 0; j < 5; j++) {
           printf("%c ", keyT[i][j]);
       }
       printf("\n");
   }
}

// Search positions of two letters
void search(char keyT[5][5], char a, char b, int arr[]) {
   if (a == 'j') a = 'i';
   if (b == 'j') b = 'i';

   for (int i = 0; i < 5; i++) {
       for (int j = 0; j < 5; j++) {
           if (keyT[i][j] == a) {
               arr[0] = i;
               arr[1] = j;
           }
           if (keyT[i][j] == b) {
               arr[2] = i;
               arr[3] = j;
           }
       }
   }
}

// Mod 5 helper
int mod5(int a) {
   return (a % 5 + 5) % 5;
}

// Prepare plaintext (insert 'x' between same letters, pad if odd length)
int prepare(char str[], int len) {
   if (len == 0) return 0;

   char temp[SIZE * 2];
   int new_len = 0;
   int i = 0;

   while (i < len) {
       temp[new_len++] = str[i];
       if (i + 1 < len) {
           if (str[i] == str[i + 1]) {
               temp[new_len++] = 'x';
               i++;
           } else {
               temp[new_len++] = str[i + 1];
               i += 2;
           }
       } else {
           i++;
       }
   }

   if (new_len % 2 != 0) {
       temp[new_len++] = 'z';
   }
   temp[new_len] = '\0';

   strcpy(str, temp);
   return new_len;
}

// Encrypt
void encrypt(char str[], char keyT[5][5], int len) {
   int arr[4];
   for (int i = 0; i < len; i += 2) {
       search(keyT, str[i], str[i + 1], arr);
       if (arr[0] == arr[2]) {  // same row
           str[i] = keyT[arr[0]][mod5(arr[1] + 1)];
           str[i + 1] = keyT[arr[2]][mod5(arr[3] + 1)];
       } else if (arr[1] == arr[3]) {  // same column
           str[i] = keyT[mod5(arr[0] + 1)][arr[1]];
           str[i + 1] = keyT[mod5(arr[2] + 1)][arr[3]];
       } else {  // rectangle
           str[i] = keyT[arr[0]][arr[3]];
           str[i + 1] = keyT[arr[2]][arr[1]];
       }
   }
}

// Decrypt
void decrypt(char str[], char keyT[5][5], int len) {
   int arr[4];
   for (int i = 0; i < len; i += 2) {
       search(keyT, str[i], str[i + 1], arr);
       if (arr[0] == arr[2]) {  // same row
           str[i] = keyT[arr[0]][mod5(arr[1] - 1)];
           str[i + 1] = keyT[arr[2]][mod5(arr[3] - 1)];
       } else if (arr[1] == arr[3]) {  // same column
           str[i] = keyT[mod5(arr[0] - 1)][arr[1]];
           str[i + 1] = keyT[mod5(arr[2] - 1)][arr[3]];
       } else {  // rectangle
           str[i] = keyT[arr[0]][arr[3]];
           str[i + 1] = keyT[arr[2]][arr[1]];
       }
   }
}

// Encrypt wrapper
void encryptByPlayfairCipher(char plaintext[], char key[], char ciphertext[]) {
   char keyT[5][5];

   int ks = strlen(key);
   toLowerCase(key, ks);
   ks = removeSpaces(key, ks);

   int ps = strlen(plaintext);
   toLowerCase(plaintext, ps);
   ps = removeSpaces(plaintext, ps);
   ps = prepare(plaintext, ps);

   generateKeyTable(key, ks, keyT);
   printKeyTable(keyT);

   strcpy(ciphertext, plaintext);
   encrypt(ciphertext, keyT, ps);
}

// Decrypt wrapper
void decryptByPlayfairCipher(char ciphertext[], char key[], char decrypted[]) {
   char keyT[5][5];

   int ks = strlen(key);
   toLowerCase(key, ks);
   ks = removeSpaces(key, ks);

   int cs = strlen(ciphertext);
   toLowerCase(ciphertext, cs);
   cs = removeSpaces(ciphertext, cs);

   generateKeyTable(key, ks, keyT);

   strcpy(decrypted, ciphertext);
   decrypt(decrypted, keyT, cs);
}

// Driver
int main() {
   char key[SIZE], plaintext[SIZE], ciphertext[SIZE], decrypted[SIZE];

   printf("Simulating Playfair Cipher\n\n");

   strcpy(key, "AI&ML Department");
   printf("Key text: %s\n", key);

   strcpy(plaintext, "HARSHINI");
   printf("Plain text: %s\n", plaintext);

   encryptByPlayfairCipher(plaintext, key, ciphertext);
   printf("\nCipher text: %s\n", ciphertext);

   decryptByPlayfairCipher(ciphertext, key, decrypted);
   printf("Decrypted text: %s\n", decrypted);

   return 0;
}
```



## Output:
<img width="404" height="463" alt="image" src="https://github.com/user-attachments/assets/40602e43-89df-4a2f-a605-7742182a3b3d" />


## RESULT:
The program implementing the Hill cipher for encryption and decryption has been successfully executed, and the results have been verified
