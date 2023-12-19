# SP
#codes from 1 to 10
Practicals in System Programming

Write a Lex program to count the number of lines and characters in the input file
%{
    #include <stdio.h>
    int line_count = 0;
    int char_count = 0;
%}


%option noyywrap


%%
\n {
    line_count ++;
    char_count ++;
}


. {
    char_count ++;
}
%%


int main() {
    yylex();
    printf("number of lines is : %d\n", line_count);
    printf("number of characters is : %d\n", char_count);


    return 0;
}




2. Write a Lex program that implements the Caesar cipher: it replaces every letter with the one three letters after in alphabetical order, wrapping around at Z. e.g. a is replaced by d, b by e, and so on z by c


%{
#include <stdio.h>
%}


%%
[a-zA-Z] {
    int shifted = yytext[0] + 3; // Shift by 3 positions
    if ((yytext[0] >= 'a' && yytext[0] <= 'z' && shifted > 'z') ||
        (yytext[0] >= 'A' && yytext[0] <= 'Z' && shifted > 'Z')) {
        yytext[0] = yytext[0] - 23; // Wrap around if needed
    } else {
        yytext[0] = shifted;
    }
    printf("%c", yytext[0]);
}


. { printf("%c", yytext[0]); }


%%


int main(void) {
    yylex();
    return 0;
}
int yywrap(){return(1);}







3. Write a Lex program that finds the longest word (defined as a contiguous string of upperand lower-case letters) in the input
%{
#include <stdio.h>
int max_length = 0;
%}


%option noyywrap


%%
[a-zA-Z]+ {
    int length = yyleng; // Get the length of the matched word
    if (length > max_length) {
        max_length = length; // Update max_length if the current word is longer
    }
}


. ; // Ignore other characters


%%


int main(void) {
    yylex();
    printf("Length of the longest word: %d\n", max_length);
    return 0;
}







4. Write a Lex program that distinguishes keywords, integers, floats, identifiers, operators, and comments in any simple programming language
%{
#include <stdio.h>
%}


%option noyywrap


%%


"if"|"else"|"while"|"for"     { printf("Keyword: %s\n", yytext); }
[0-9]+                         { printf("Integer: %s\n", yytext); }
[0-9]+"."[0-9]*                { printf("Float: %s\n", yytext); }
[a-zA-Z_][a-zA-Z0-9_]*         { printf("Identifier: %s\n", yytext); }
"+"|"-"|"*"|"/"|"="|"=="|"<"|">"|"<="|">="    { printf("Operator: %s\n", yytext); }
"//"(.)*("\n"|EOF)              { /* Ignore single-line comments */ }
"/*"([^*]|"*"+[^*/])*"*"+"/"   { /* Ignore multi-line comments */ }


. { /* Ignore other characters */ }


%%


int main(void) {
    yylex();
    return 0;
}









5. Write a Lex program to count the number of identifiers in a C file.
%{
    #include <stdio.h>
    int count = 0;
%}


%option noyywrap


%%
[a-zA-Z_][a-zA-Z0-9_]* {
    count++;
 }


 . ; //to ignore other patterns


%%


int main(void) {
    yylex();
    printf("Number of identifiers is : %d\n", count);
    return 0;
}







6. Write a Lex program to count the number of words, characters, blank spaces and lines in a C file.
%{
    #include <stdio.h>  
    int count_words=0;
    int count_character=0;
    int count_space=0;
    int count_line = 0;
%}


%option noyywrap


%%
[a-zA-Z]+ {
    count_words++;
    count_character += yyleng;
}
[ \t\r\f\v]+ {
    count_space++;
}
[\n] {
    count_line++;
    count_character++;
}
. {
    count_character++;
}
%%


int main() {
    yylex();


    printf("Number of words are : %d\n", count_words);
    printf("Number of blank spaces are : %d\n", count_space);
    printf("Number of lines are : %d\n", count_line);
    printf("Number of characters are : %d\n", count_character);


    return 0;
}



7. Write a Lex specification program that generates a C program which takes a string “abcd” and prints the following output :
abcd 
abc
ab
a
%{
    #include <stdio.h>
%}


%option noyywrap


%%
[a-z]* {
    int length = yyleng;
    for(int j=0; j<length; j++) {
        for(int i=0; i<=length-j-1; i++) {
            printf("%c", yytext[i]);
        }
    printf("\n");
    }
}


. ; //ignore other characters


%%


int main(void) {
    yylex();
    return 0;
}





8. A program in Lex to recognize a valid arithmetic expression.
%{
    #include <stdio.h>
    int valid = 1;    
%}


%option noyywrap


%%
[0-9]+ {
    //valid
}
[+-/*] {
    //valid
}
[(] {
    //valid
}
[)] {
    //valid
}
\n {
    //ignore any new lines
}
. {
    //if any other character is encountered the expression becomes invalid
    valid = 0;
}
%%


int main() {
    yylex();


    if(valid) printf("This is a valid arithmetic expression \n");
    else printf("This is an invalid arithmetic expression \n");


    return 0;
}


9. Write a YACC program to find the validity of a given expression (for operators + - * and /)
%{
#include <stdio.h>
int yylex(void);
void yyerror(const char *s);
%}


%token NUMBER
%left '+' '-'
%left '*' '/'


%%


expression: expression '+' expression
          | expression '-' expression
          | expression '*' expression
          | expression '/' expression
          | '(' expression ')'
          | NUMBER


%%


int main() {
    yyparse();
    return 0;
}


int yylex() {
    int c = getchar();


    if (c == '+' || c == '-' || c == '*' || c == '/' || c == '(' || c == ')')
        return c;
    else if (c >= '0' && c <= '9')
        return NUMBER;
    else if (c == '\n' || c == EOF) 
        return 0;  // End of input
    else
        return -1; // Invalid character
}


void yyerror(const char *s) {
    printf("Error: %s\n", s);


10. A Program in YACC which recognizes a valid variable which starts with letter followed by a digit. The letter should be in lowercase only.
%{
#include <stdio.h>
int yylex(void);
void yyerror(const char *s);
%}


%token LOWERCASE_LETTER DIGIT


%%


input: variable '\n'
     | input variable '\n'
     ;


variable: LOWERCASE_LETTER DIGIT {
            printf("Valid variable: %c%d\n", $1, $2);
          }
        ;


%%


int main(void) {
    yyparse();
    return 0;
}


int yylex(void) {
    int c = getchar();


    if (c >= 'a' && c <= 'z') {
        // Convert to uppercase letter
        return LOWERCASE_LETTER;
    } else if (c >= '0' && c <= '9') {
        // Convert to digit
        return DIGIT;
    } else if (c == '\n' || c == EOF) {
        return 0;  // End of input
    } else if (c == ' ' || c == '\t') {
        return yylex();  // Skip spaces and tabs
    } else {
        return -1; // Invalid character
    }
}


void yyerror(const char *s) {
    printf("Error: %s\n", s);
}






