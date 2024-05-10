# Ex. No : 1	
## CONVERSION OF NON-DETERMINISTIC FINITE AUTOMATON (NFA) TO DETERMINISTIC FINITE AUTOMATON (DFA)
## Register Number : 212223220017
## Date : 08/02/2024
## AIM   
To write a C program for Conversion of Non-Deterministic Finite Automaton (NFA) To Deterministic Finite Automaton (DFA).

## ALGORITHM
Step 1 : Take ∈ closure for the beginning state of NFA as beginning state of DFA. 

Step 2 : Find the states that can be traversed from the present for each input symbol (union of transition value and their closures for each states of NFA present in current state of DFA). 

Step 3 : If any new state is found take it as current state and repeat step 2. 

Step 4 : Do repeat Step 2 and Step 3 until no new state present in DFA transition table. 

Step 5 : Mark the states of DFA which contains final state of NFA as final states of DFA.

## PROGRAM
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX_LEN 100
 
char NFA_FILE[MAX_LEN];
char buffer[MAX_LEN];
int zz = 0;
 
// Structure to store DFA states and their
// status ( i.e new entry or already present)
struct DFA {
    char *states;
    int count;
} dfa;
 
int last_index = 0;
FILE *fp;
int symbols;
 
void reset(int ar[], int size) {
    int i;
    for (i = 0; i < size; i++) {
        ar[i] = 0;
    }
}

void check(int ar[], char S[]) {
    int i, j;
    int len = strlen(S);
    for (i = 0; i < len; i++) {
        j = ((int)(S[i]) - 65);
        ar[j]++;
    }
}
 
void state(int ar[], int size, char S[]) {
    int j, k = 0;
    for (j = 0; j < size; j++) {
    if (ar[j] != 0)
        S[k++] = (char)(65 + j);
    }
    S[k] = '\0';
}
 
int closure(int ar[], int size) {
    int i;
    for (i = 0; i < size; i++) {
    if (ar[i] == 1)
        return i;
    }
    return (100);
}
 
int indexing(struct DFA *dfa) {
    int i;
    for (i = 0; i < last_index; i++) {
    if (dfa[i].count == 0)
        return 1;
    }
    return -1;
}

void Display_closure(int states, int closure_ar[],char *closure_table[], char *NFA_TABLE[][symbols + 1], char *DFA_TABLE[][symbols]) {
    int i;
    for (i = 0; i < states; i++) {
        reset(closure_ar, states);
        closure_ar[i] = 2;
        if (strcmp(&NFA_TABLE[i][symbols], "-") != 0) {
            strcpy(buffer, &NFA_TABLE[i][symbols]);
            check(closure_ar, buffer);
            int z = closure(closure_ar, states);
 
            while (z != 100)
            {
                if (strcmp(&NFA_TABLE[z][symbols], "-") != 0) {
                    strcpy(buffer, &NFA_TABLE[z][symbols]);
                    check(closure_ar, buffer);
                }
                closure_ar[z]++;
                z = closure(closure_ar, states);
            }
        }
        printf("\n e-Closure (%c) :\t", (char)(65 + i));
        bzero((void *)buffer, MAX_LEN);
        state(closure_ar, states, buffer);
        strcpy(&closure_table[i], buffer);
        printf("%s\n", &closure_table[i]);
    }
}
 
int new_states(struct DFA *dfa, char S[]) {
 
    int i;
    for (i = 0; i < last_index; i++) {
        if (strcmp(&dfa[i].states, S) == 0)
        return 0;
    }
    strcpy(&dfa[last_index++].states, S);
    dfa[last_index - 1].count = 0;
    return 1;
}

void trans(char S[], int M, char *clsr_t[], int st, char *NFT[][symbols + 1], char TB[]) {
    int len = strlen(S);
    int i, j, k, g;
    int arr[st];
    int sz;
    reset(arr, st);
    char temp[MAX_LEN], temp2[MAX_LEN];
    char *buff;
    
    // Transition function from NFA to DFA
    for (i = 0; i < len; i++) {
    
    j = ((int)(S[i] - 65));
    strcpy(temp, &NFT[j][M]);
    
    if (strcmp(temp, "-") != 0) {
    sz = strlen(temp);
    g = 0;
    
    while (g < sz) {
    k = ((int)(temp[g] - 65));
    strcpy(temp2, &clsr_t[k]);
    check(arr, temp2);
    g++;
    }
    }
    }
    
    bzero((void *)temp, MAX_LEN);
    state(arr, st, temp);
    if (temp[0] != '\0') {
    strcpy(TB, temp);
    } else
    strcpy(TB, "-");
}
 
/* Display DFA transition state table*/
void Display_DFA(int last_index, struct DFA *dfa_states,
                 char *DFA_TABLE[][symbols]) {
  int i, j;
  printf("\n\n********************************************************\n\n");
  printf("\t\t DFA TRANSITION STATE TABLE \t\t \n\n");
  printf("\n STATES OF DFA :\t\t");
 
  for (i = 1; i < last_index; i++)
    printf("%s, ", &dfa_states[i].states);
  printf("\n");
  printf("\n GIVEN SYMBOLS FOR DFA: \t");
 
  for (i = 0; i < symbols; i++)
    printf("%d, ", i);
  printf("\n\n");
  printf("STATES\t");
 
  for (i = 0; i < symbols; i++)
    printf("|%d\t", i);
  printf("\n");
 
  // display the DFA transition state table
  printf("--------+-----------------------\n");
  for (i = 0; i < zz; i++) {
    printf("%s\t", &dfa_states[i + 1].states);
    for (j = 0; j < symbols; j++) {
      printf("|%s \t", &DFA_TABLE[i][j]);
    }
    printf("\n");
  }
}


 
// Driver Code
int main() {
  int i, j, states;
  char T_buf[MAX_LEN];
 
  // creating an array dfa structures
  struct DFA *dfa_states = malloc(MAX_LEN * (sizeof(dfa)));
  states = 6, symbols = 2;
 
  printf("\n STATES OF NFA :\t\t");
  for (i = 0; i < states; i++)
 
    printf("%c, ", (char)(65 + i));
  printf("\n");
  printf("\n GIVEN SYMBOLS FOR NFA: \t");
 
  for (i = 0; i < symbols; i++)
 
    printf("%d, ", i);
  printf("eps");
  printf("\n\n");
  char *NFA_TABLE[states][symbols + 1];
 
  // Hard coded input for NFA table
  char *DFA_TABLE[MAX_LEN][symbols];
  strcpy(&NFA_TABLE[0][0], "FC");
  strcpy(&NFA_TABLE[0][1], "-");
  strcpy(&NFA_TABLE[0][2], "BF");
  strcpy(&NFA_TABLE[1][0], "-");
  strcpy(&NFA_TABLE[1][1], "C");
  strcpy(&NFA_TABLE[1][2], "-");
  strcpy(&NFA_TABLE[2][0], "-");
  strcpy(&NFA_TABLE[2][1], "-");
  strcpy(&NFA_TABLE[2][2], "D");
  strcpy(&NFA_TABLE[3][0], "E");
  strcpy(&NFA_TABLE[3][1], "A");
  strcpy(&NFA_TABLE[3][2], "-");
  strcpy(&NFA_TABLE[4][0], "A");
  strcpy(&NFA_TABLE[4][1], "-");
  strcpy(&NFA_TABLE[4][2], "BF");
  strcpy(&NFA_TABLE[5][0], "-");
  strcpy(&NFA_TABLE[5][1], "-");
  strcpy(&NFA_TABLE[5][2], "-");
  printf("\n NFA STATE TRANSITION TABLE \n\n\n");
  printf("STATES\t");
 
  for (i = 0; i < symbols; i++)
    printf("|%d\t", i);
  printf("eps\n");
   // Displaying the matrix of NFA transition table
  printf("--------+------------------------------------\n");
  for (i = 0; i < states; i++) {
    printf("%c\t", (char)(65 + i));
     for (j = 0; j <= symbols; j++) {
      printf("|%s \t", &NFA_TABLE[i][j]);
    }
    printf("\n");
  }
  int closure_ar[states];
  char *closure_table[states];
   Display_closure(states, closure_ar, closure_table, NFA_TABLE, DFA_TABLE);
  strcpy(&dfa_states[last_index++].states, "-");
   dfa_states[last_index - 1].count = 1;
  bzero((void *)buffer, MAX_LEN);
   strcpy(buffer, &closure_table[0]);
  strcpy(&dfa_states[last_index++].states, buffer);
   int Sm = 1, ind = 1;
  int start_index = 1;
   // Filling up the DFA table with transition values
  // Till new states can be entered in DFA table
  while (ind != -1) {
    dfa_states[start_index].count = 1;
    Sm = 0;
    for (i = 0; i < symbols; i++) {
 
      trans(buffer, i, closure_table, states, NFA_TABLE, T_buf);
 
      // storing the new DFA state in buffer
      strcpy(&DFA_TABLE[zz][i], T_buf);

      // parameter to control new states
      Sm = Sm + new_states(dfa_states, T_buf);
    }
    ind = indexing(dfa_states);
    if (ind != -1)
      strcpy(buffer, &dfa_states[++start_index].states);
    zz++;
  }
  // display the DFA TABLE
  Display_DFA(last_index, dfa_states, DFA_TABLE);
 
  return 0;
}
```
## OUTPUT 
![image](https://github.com/dharshini-29/19CS409-Compiler-Design-Lab/assets/147474632/b0422cdd-820b-4179-a7fb-a9579021f705)

## RESULT
Program to convert NFA to DFA has been implemented.




