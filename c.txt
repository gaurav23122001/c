// To run lex program 
flex hello.l
gcc lex.yy.c 
./a.out

// to run YACC program
flex hello.l
bison -d hello.y
gcc lex.yy.c y.tab.c
./a.out

// C program to accept string under a*,a*b+,abb

#include <stdio.h>
#include <stdlib.h>
#include <string.h>


void main() {
    char s[20], c;
    int state = 0, i = 0;
    printf("Enter a string: ");
    gets(s);
    while (s[i] != '\0') {
        switch (state) {
        case 0:
            c = s[i++];
            if (c == 'a')
                state = 1;
            else if (c == 'b')
                state = 2;
            else
                state = 6;
            break;
        case 1:
            c = s[i++];
            if (c == 'a')
                state = 3;
            else if (c == 'b')
                state = 4;
            else
                state = 6;
            break;
        case 2:
            c = s[i++];
            if (c == 'a')
                state = 6;
            else if (c == 'b')
                state = 2;
            else
                state = 6;
            break;
        case 3:
            c = s[i++];
            if (c == 'a')
                state = 3;
            else if (c == 'b')
                state = 2;
            else
                state = 6;
            break;
        case 4:
            c = s[i++];
            if (c == 'a')
                state = 6;
            else if (c == 'b')
                state = 5;
            else
                state = 6;
            break;
        case 5:
            c = s[i++];
            if (c == 'a')
                state = 6;
            else if (c == 'b')
                state = 2;
            else
                state = 6;
            break;
        case 6:
            printf("%s is not recognised", s); 
            exit(0);
        }
    }
    if (state == 1 || state == 0 || state == 3)
        printf("%s is accepted under rule 'a*'", s);
    else if ((state == 2) || (state == 4))
        printf("%s is accepted under rule 'a*b+'", s);
    else if (state == 5)
        printf("%s is accepted under rule 'abb'", s);
    else 
        printf("%s is not recognised", s);
}

// yacc program to accept strings that start with 0 or 1

Lexical Analyzer Source Code :
%{
  extern int yylval;
%}

%%  
0 {yylval = 0; return ZERO;}
1 {yylval = 1; return ONE;}
.|\n {yylval = 2; return 0;}
%%

Parser Source Code :

%{
#include<stdio.h>
#include <stdlib.h>
void yyerror(const char *str)
{
printf("\nSequence Rejected\n");		
}

%}

%token ZERO ONE
%%

r : s {printf("\nSequence Accepted\n\n");}
;

s : n
| ZERO a
| ONE b
;

a : n a
| ZERO
;

b : n b
| ONE
;

n : ZERO
| ONE
;

%%

#include"lex.yy.c"
int main()
{
	printf("\nEnter Sequence of Zeros and Ones : ");
	yyparse();
	printf("\n");
	return 0;
}

// yacc to program to implement a calculator and recognize a valid Arithmetic operation

Lexical Analyzer Source Code:
%{
#include<stdio.h>
#include "y.tab.h"
extern int yylval;
%}
%%
[0-9]+ {
		yylval=atoi(yytext);
		return NUMBER;

	}
[\t] ;

[\n] return 0;

. return yytext[0];

%%

int yywrap()
{
return 1;
}

Parser Source Code:
%{
#include<stdio.h>
int flag=0;
%}

%token NUMBER

%left '+' '-'

%left '*' '/' '%'

%left '(' ')'

%%

ArithmeticExpression: E{

		printf("\nResult=%d\n", $$);

		return 0;

		};
E:E'+'E {$$=$1+$3;}

|E'-'E {$$=$1-$3;}

|E'*'E {$$=$1*$3;}

|E'/'E {$$=$1/$3;}

|E'%'E {$$=$1%$3;}

|'('E')' {$$=$2;}

| NUMBER {$$=$1;}

;

%%
void main()
{
printf("\nEnter Any Arithmetic Expression which
				can have operations Addition,
				Subtraction, Multiplication, Division,
						Modulus and Round brackets:\n");

yyparse();
if(flag==0)
printf("\nEntered arithmetic expression is Valid\n\n");
}

void yyerror()
{
printf("\nEntered arithmetic expression is Invalid\n\n");
flag=1;
}


//WAP to design a lexical analyzer

#include<string.h>
#include<ctype.h>
#include<stdio.h>
void keyword(char str[10])
{
    if(strcmp("for",str)==0||strcmp("while",str)==0||strcmp("do",str)==0||strcmp("int",str )==0||strcmp("float",str)==0||strcmp("char",str)==0||strcmp("double",str)==0||strcmp("static",str)==0||strcmp("switch",str)==0||strcmp("case",str)==0)
        printf("\n%s is a keyword",str);
    else
        printf("\n%s is an identifier",str);
}
int main()
{
    FILE *f1,*f2,*f3;
    char c,str[10],st1[10];
    int num[100],lineno=0,tokenvalue=0,i=0,j=0,k=0;
    printf("\nEnter the c Program: ");/*gets(st1);*/
    f1=fopen("input","w");
    while((c=getchar())!=EOF)
        putc(c,f1);
    fclose(f1);
    f1=fopen("input","r");
    f2=fopen("identifier","w");
    f3=fopen("specialchar","w");
    while((c=getc(f1))!=EOF)
    {
        if(isdigit(c))
        {
            tokenvalue=c-'0';
            c=getc(f1);
            while(isdigit(c))
            {
                tokenvalue*=10+c-'0';
                c=getc(f1);
            }
            num[i++]=tokenvalue;
            ungetc(c,f1);
        }
        else if(isalpha(c))
        {
            putc(c,f2);
            c=getc(f1);
            while(isdigit(c)||isalpha(c)||c=='_'||c=='$')
            {
                putc(c,f2);
                c=getc(f1);
            }
            putc(' ',f2);
            ungetc(c,f1);
        }
        else if(c==' '||c=='\t')
            printf(" ");
        else if(c=='\n')
            lineno++;
        else
            putc(c,f3);
    }
    fclose(f2);
    fclose(f3);
    fclose(f1);
    printf("\nThe no's in the program are");
    for(j=0; j<i; j++)
        printf("%d",num[j]);
    printf("\n");
    f2=fopen("identifier","r");
    k=0;
    printf("The keywords and identifiersare:");
    while((c=getc(f2))!=EOF)
    {
        if(c!=' ')
            str[k++]=c;
        else
        {
            str[k]='\0';
            keyword(str);
            k=0;
        }
    }
    fclose(f2);
    f3=fopen("specialchar","r");
    printf("\nSpecial characters are");
    while((c=getc(f3))!=EOF)
        printf("%c",c);
    printf("\n");
    fclose(f3);
    printf("Total no. of lines are:%d",lineno);
    return 0;
}

//WAP to detect tokens in a program

#include <stdbool.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

bool isDelimiter(char ch)
{
	if (ch == ' ' || ch == '+' || ch == '-' || ch == '*' ||
		ch == '/' || ch == ',' || ch == ';' || ch == '>' ||
		ch == '<' || ch == '=' || ch == '(' || ch == ')' ||
		ch == '[' || ch == ']' || ch == '{' || ch == '}')
		return (true);
	return (false);
}

bool isOperator(char ch)
{
	if (ch == '+' || ch == '-' || ch == '*' ||
		ch == '/' || ch == '>' || ch == '<' ||
		ch == '=')
		return (true);
	return (false);
}
bool validIdentifier(char* str)
{
	if (str[0] == '0' || str[0] == '1' || str[0] == '2' ||
		str[0] == '3' || str[0] == '4' || str[0] == '5' ||
		str[0] == '6' || str[0] == '7' || str[0] == '8' ||
		str[0] == '9' || isDelimiter(str[0]) == true)
		return (false);
	return (true);
}

bool isKeyword(char* str)
{
	if (!strcmp(str, "if") || !strcmp(str, "else") ||
		!strcmp(str, "while") || !strcmp(str, "do") ||
		!strcmp(str, "break") ||
		!strcmp(str, "continue") || !strcmp(str, "int")
		|| !strcmp(str, "double") || !strcmp(str, "float")
		|| !strcmp(str, "return") || !strcmp(str, "char")
		|| !strcmp(str, "case") || !strcmp(str, "char")
		|| !strcmp(str, "sizeof") || !strcmp(str, "long")
		|| !strcmp(str, "short") || !strcmp(str, "typedef")
		|| !strcmp(str, "switch") || !strcmp(str, "unsigned")
		|| !strcmp(str, "void") || !strcmp(str, "static")
		|| !strcmp(str, "struct") || !strcmp(str, "goto"))
		return (true);
	return (false);
}

bool isInteger(char* str)
{
	int i, len = strlen(str);

	if (len == 0)
		return (false);
	for (i = 0; i < len; i++) {
		if (str[i] != '0' && str[i] != '1' && str[i] != '2'
			&& str[i] != '3' && str[i] != '4' && str[i] != '5'
			&& str[i] != '6' && str[i] != '7' && str[i] != '8'
			&& str[i] != '9' || (str[i] == '-' && i > 0))
			return (false);
	}
	return (true);
}
bool isRealNumber(char* str)
{
	int i, len = strlen(str);
	bool hasDecimal = false;

	if (len == 0)
		return (false);
	for (i = 0; i < len; i++) {
		if (str[i] != '0' && str[i] != '1' && str[i] != '2'
			&& str[i] != '3' && str[i] != '4' && str[i] != '5'
			&& str[i] != '6' && str[i] != '7' && str[i] != '8'
			&& str[i] != '9' && str[i] != '.' ||
			(str[i] == '-' && i > 0))
			return (false);
		if (str[i] == '.')
			hasDecimal = true;
	}
	return (hasDecimal);
}

char* subString(char* str, int left, int right)
{
	int i;
	char* subStr = (char*)malloc(
				sizeof(char) * (right - left + 2));

	for (i = left; i <= right; i++)
		subStr[i - left] = str[i];
	subStr[right - left + 1] = '\0';
	return (subStr);
}

void parse(char* str)
{
	int left = 0, right = 0;
	int len = strlen(str);

	while (right <= len && left <= right) {
		if (isDelimiter(str[right]) == false)
			right++;

		if (isDelimiter(str[right]) == true && left == right) {
			if (isOperator(str[right]) == true)
				printf("'%c' IS AN OPERATOR\n", str[right]);

			right++;
			left = right;
		} else if (isDelimiter(str[right]) == true && left != right
				|| (right == len && left != right)) {
			char* subStr = subString(str, left, right - 1);

			if (isKeyword(subStr) == true)
				printf("'%s' IS A KEYWORD\n", subStr);

			else if (isInteger(subStr) == true)
				printf("'%s' IS AN INTEGER\n", subStr);

			else if (isRealNumber(subStr) == true)
				printf("'%s' IS A REAL NUMBER\n", subStr);

			else if (validIdentifier(subStr) == true
					&& isDelimiter(str[right - 1]) == false)
				printf("'%s' IS A VALID IDENTIFIER\n", subStr);

			else if (validIdentifier(subStr) == false
					&& isDelimiter(str[right - 1]) == false)
				printf("'%s' IS NOT A VALID IDENTIFIER\n", subStr);
			left = right;
		}
	}
	return;
}
int main()
{
	// maximum length of string is 100 here
	char str[100] = "int a = b + 1c; ";

	parse(str); // calling the parse function

	return (0);
}

// program to implement shift reduce parser
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int z = 0, i = 0, j = 0, c = 0;

char a[16], ac[20], stk[15], act[10];

void check()
{
    strcpy(ac, "REDUCE TO E -> ");

    for (z = 0; z < c; z++)
    {

        if (stk[z] == '4')
        {
            printf("%s4", ac);
            stk[z] = 'E';
            stk[z + 1] = '\0';

            printf("\n$%s\t%s$\t", stk, a);
        }
    }

    for (z = 0; z < c - 2; z++)
    {
        if (stk[z] == '2' && stk[z + 1] == 'E' &&
            stk[z + 2] == '2')
        {
            printf("%s2E2", ac);
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 2] = '\0';
            printf("\n$%s\t%s$\t", stk, a);
            i = i - 2;
        }
    }

    for (z = 0; z < c - 2; z++)
    {

        if (stk[z] == '3' && stk[z + 1] == 'E' &&
            stk[z + 2] == '3')
        {
            printf("%s3E3", ac);
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 1] = '\0';
            printf("\n$%s\t%s$\t", stk, a);
            i = i - 2;
        }
    }
    return;
}


int main()
{
    printf("GRAMMAR is -\nE->2E2 \nE->3E3 \nE->4\n");

    // a is input string
    strcpy(a, "32423");

    c = strlen(a);

    strcpy(act, "SHIFT");

    printf("\nstack \t input \t action");

    printf("\n$\t%s$\t", a);

    for (i = 0; j < c; i++, j++)
    {

        printf("%s", act);

        stk[i] = a[j];
        stk[i + 1] = '\0';

        a[j] = ' ';

        printf("\n$%s\t%s$\t", stk, a);

        check();
    }

    check();

    if (stk[0] == 'E' && stk[1] == '\0')
        printf("Accept\n");
    else
        printf("Reject\n");
}

// WAP to implement operator precedence parsing
// input string: i*(i+i)*i
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char *input;
int i = 0;
char lasthandle[6], stack[50], handles[][5] = {")E(", "E*E", "E+E", "i", "E^E"};
//(E) becomes )E( when pushed to stack

int top = 0, l;
char prec[9][9] = {

    /*input*/

    /*stack    +    -   *   /   ^   i   (   )   $  */

    /*  + */ '>','>','<','<','<','<','<','>','>',

    /*  - */ '>','>','<','<','<','<','<','>','>',

    /*  * */ '>','>','>','>', '<', '<', '<', '>', '>',

    /*  / */ '>','>','>','>','<','<','<','>','>',

    /*  ^ */ '>','>','>','>','<','<','<','>','>',

    /*  i */ '>','>','>','>','>','e','e','>','>',

    /*  ( */ '<','<','<','<','<','<','<','>','e',

    /*  ) */ '>', '>', '>', '>', '>', 'e', 'e', '>', '>',

    /*  $ */ '<', '<', '<', '<', '<', '<', '<', '<', '>',

};

int getindex(char c)
{
    switch (c)
    {
    case '+':
        return 0;
    case '-':
        return 1;
    case '*':
        return 2;
    case '/':
        return 3;
    case '^':
        return 4;
    case 'i':
        return 5;
    case '(':
        return 6;
    case ')':
        return 7;
    case '$':
        return 8;
    }
}

int shift()
{
    stack[++top] = *(input + i++);
    stack[top + 1] = '\0';
}

int reduce()
{
    int i, len, found, t;
    for (i = 0; i < 5; i++) // selecting handles
    {
        len = strlen(handles[i]);
        if (stack[top] == handles[i][0] && top + 1 >= len)
        {
            found = 1;
            for (t = 0; t < len; t++)
            {
                if (stack[top - t] != handles[i][t])
                {
                    found = 0;
                    break;
                }
            }
            if (found == 1)
            {
                stack[top - t + 1] = 'E';
                top = top - t + 1;
                strcpy(lasthandle, handles[i]);
                stack[top + 1] = '\0';
                return 1; // successful reduction
            }
        }
    }
    return 0;
}

void dispstack()
{
    int j;
    for (j = 0; j <= top; j++)
        printf("%c", stack[j]);
}

void dispinput()
{
    int j;
    for (j = i; j < l; j++)
        printf("%c", *(input + j));
}

void main()
{
    int j;

    input = (char *)malloc(50 * sizeof(char));
    printf("\nEnter the string\n");
    scanf("%s", input);
    input = strcat(input, "$");
    l = strlen(input);
    strcpy(stack, "$");
    printf("\nSTACK\tINPUT\tACTION");
    while (i <= l)
    {
        shift();
        printf("\n");
        dispstack();
        printf("\t");
        dispinput();
        printf("\tShift");
        if (prec[getindex(stack[top])][getindex(input[i])] == '>')
        {
            while (reduce())
            {
                printf("\n");
                dispstack();
                printf("\t");
                dispinput();
                printf("\tReduced: E->%s", lasthandle);
            }
        }
    }

    if (strcmp(stack, "$E$") == 0)
        printf("\nAccepted;");
    else
        printf("\nNot Accepted;");
}

// WAP to implement recursive descent parsing
// Input String : abab$
#include <stdio.h>
#include <stdlib.h>
char l;

void match(char c)
{
    if (l == c)
        l = getchar();
    else
    {
        printf("Invalid Input\n");
        exit(0);
    }
}

void B()
{
    if (l == 'b')
    {
        match('b');
    }
    else
    {
        printf("Invalid Input\n");
        exit(0);
    }
}

void A()
{
    if (l == 'a')
    {
        match('a');
        B();
    }
    else
        return;
}

void S()
{
    A();
    A();
}

void main()
{
    char input[10];
    printf("Enter String with $ at the end\n");
    l = getchar();
    S();
    if (l == '$')
    {
        printf("\nParsing Successful\n");
    }
    else
    {
        printf("Invalid Input\n");
    }
}

// WAP to implement LL1 Parsing

#include <stdio.h>
#include <string.h>

char prol[7][10] = {"S", "A", "A", "B", "B", "C", "C"};
char pror[7][10] = {"A", "Bb", "Cd", "aB", "@", "Cc", "@"};
char prod[7][10] = {"S->A", "A->Bb", "A->Cd", "B->aB", "B->@", "C->Cc", "C->@"};
char first[7][10] = {"abcd", "ab", "cd", "a@", "@", "c@", "@"};
char follow[7][10] = {"$", "$", "$", "a$", "b$", "c$", "d$"};
char table[5][6][10];

int numr(char c)
{
    switch (c)
    {
    case 'S':
        return 0;
    case 'A':
        return 1;
    case 'B':
        return 2;
    case 'C':
        return 3;
    case 'a':
        return 0;
    case 'b':
        return 1;
    case 'c':
        return 2;
    case 'd':
        return 3;
    case '$':
        return 4;
    }
    return (2);
}
void main()
{
    int i, j, k;
    for (i = 0; i < 5; i++)
        for (j = 0; j < 6; j++)
            strcpy(table[i][j], " ");
    printf("\nThe following is the predictive parsing table for the following grammar:\n");
    for (i = 0; i < 7; i++)
        printf("%s\n", prod[i]);
    printf("\nPredictive parsing table is\n");
    fflush(stdin);
    for (i = 0; i < 7; i++)
    {
        k = strlen(first[i]);
        for (j = 0; j < 10; j++)
            if (first[i][j] != '@')
                strcpy(table[numr(prol[i][0]) + 1][numr(first[i][j]) + 1], prod[i]);
    }
    for (i = 0; i < 7; i++)
    {
        if (strlen(pror[i]) == 1)
        {
            if (pror[i][0] == '@')
            {
                k = strlen(follow[i]);
                for (j = 0; j < k; j++)
                    strcpy(table[numr(prol[i][0]) + 1][numr(follow[i][j]) + 1], prod[i]);
            }
        }
    }
    strcpy(table[0][0], " ");
    strcpy(table[0][1], "a");
    strcpy(table[0][2], "b");
    strcpy(table[0][3], "c");
    strcpy(table[0][4], "d");
    strcpy(table[0][5], "$");
    strcpy(table[1][0], "S");
    strcpy(table[2][0], "A");
    strcpy(table[3][0], "B");
    strcpy(table[4][0], "C");
    printf("\n--------------------------------------------------------\n");
    for (i = 0; i < 5; i++)
        for (j = 0; j < 6; j++)
        {
            printf("%-10s", table[i][j]);
            if (j == 5)
                printf("\n--------------------------------------------------------\n");
        }

}


// WAP to implement LALR bottom-up parser
// input string: i+i*i
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void push(char *, int *, char);

char stacktop(char *);

void isproduct(char, char);

int ister(char);

int isnter(char);

int isstate(char);

void error();

void isreduce(char, char);

char pop(char *, int *);

void printt(char *, int *, char[], int);

void rep(char[], int);

struct action
{
    char row[6][5];
};

const struct action A[12] = {

    {"sf", "emp", "emp", "se", "emp", "emp"},

    {"emp", "sg", "emp", "emp", "emp", "acc"},

    {"emp", "rc", "sh", "emp", "rc", "rc"},

    {"emp", "re", "re", "emp", "re", "re"},

    {"sf", "emp", "emp", "se", "emp", "emp"},

    {"emp", "rg", "rg", "emp", "rg", "rg"},

    {"sf", "emp", "emp", "se", "emp", "emp"},

    {"sf", "emp", "emp", "se", "emp", "emp"},

    {"emp", "sg", "emp", "emp", "sl", "emp"},

    {"emp", "rb", "sh", "emp", "rb", "rb"},

    {"emp", "rb", "rd", "emp", "rd", "rd"},

    {"emp", "rf", "rf", "emp", "rf", "rf"}

};

struct gotol

{

    char r[3][4];
};

const struct gotol G[12] = {

    {"b", "c", "d"},

    {"emp", "emp", "emp"},

    {"emp", "emp", "emp"},

    {"emp", "emp", "emp"},

    {"i", "c", "d"},

    {"emp", "emp", "emp"},

    {"emp", "j", "d"},

    {"emp", "emp", "k"},

    {"emp", "emp", "emp"},

    {"emp", "emp", "emp"},

};

char ter[6] = {'i', '+', '*', ')', '(', '$'};

char nter[3] = {'E', 'T', 'F'};

char states[12] = {'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'm', 'j', 'k', 'l'};

char stack[100];

int top = -1;

char temp[10];

struct grammar

{
    char left;

    char right[5];
};

const struct grammar rl[6] = {

    {'E', "e+T"},

    {'E', "T"},

    {'T', "T*F"},

    {'T', "F"},

    {'F', "(E)"},

    {'F', "i"},

};

void main()

{

    char inp[80], x, p, dl[80], y, bl = 'a';

    int i = 0, j, k, l, n, m, c, len;

    printf(" Enter the input :");

    scanf("%s", inp);

    len = strlen(inp);

    inp[len] = '$';

    inp[len + 1] = '\0';

    push(stack, &top, bl);

    printf("\n stack \t\t\t input");

    printt(stack, &top, inp, i);

    do

    {

        x = inp[i];

        p = stacktop(stack);

        isproduct(x, p);

        if (strcmp(temp, "emp") == 0)

            error();

        if (strcmp(temp, "acc") == 0)

            break;

        else

        {

            if (temp[0] == 's')

            {

                push(stack, &top, inp[i]);

                push(stack, &top, temp[1]);

                i++;
            }

            else

            {

                if (temp[0] == 'r')

                {

                    j = isstate(temp[1]);

                    strcpy(temp, rl[j - 2].right);

                    dl[0] = rl[j - 2].left;

                    dl[1] = '\0';

                    n = strlen(temp);

                    for (k = 0; k < 2 * n; k++)

                        pop(stack, &top);

                    for (m = 0; dl[m] != '\0'; m++)

                        push(stack, &top, dl[m]);

                    l = top;

                    y = stack[l - 1];

                    isreduce(y, dl[0]);

                    for (m = 0; temp[m] != '\0'; m++)

                        push(stack, &top, temp[m]);
                }
            }
        }

        printt(stack, &top, inp, i);

    } while (inp[i] != '\0');

    if (strcmp(temp, "acc") == 0)

        printf(" \nInput accepted\n");

    else

        printf(" \nInput rejected\n");
}
void push(char *s, int *sp, char item)

{

    if (*sp == 100)

        printf(" stack is full ");

    else

    {

        *sp = *sp + 1;
        s[*sp] = item;
    }
}

char stacktop(char *s)

{

    char i;

    i = s[top];

    return i;
}

void isproduct(char x, char p)

{

    int k, l;

    k = ister(x);

    l = isstate(p);

    strcpy(temp, A[l - 1].row[k - 1]);
}

int ister(char x)

{

    int i;

    for (i = 0; i < 6; i++)

        if (x == ter[i])

            return i + 1;

    return 0;
}
int isnter(char x)

{

    int i;

    for (i = 0; i < 3; i++)

        if (x == nter[i])

            return i + 1;

    return 0;
}

int isstate(char p)

{

    int i;

    for (i = 0; i < 12; i++)

        if (p == states[i])
            return i + 1;

    return 0;
}

void error()

{

    printf(" error in the input ");

    exit(0);
}

void isreduce(char x, char p)

{

    int k, l;

    k = isstate(x);

    l = isnter(p);

    strcpy(temp, G[k - 1].r[l - 1]);
}
char pop(char *s, int *sp)

{

    char item;

    if (*sp == -1)

        printf(" stack is empty ");

    else

    {

        item = s[*sp];

        *sp = *sp - 1;
    }

    return item;
}

void printt(char *t, int *p, char inp[], int i)

{

    int r;

    printf("\n");

    for (r = 0; r <= *p; r++)

        rep(t, r);

    printf("\t\t\t");

    for (r = i; inp[r] != '\0'; r++)
        printf("%c", inp[r]);
}
void rep(char t[], int r)

{

    char c;

    c = t[r];

    switch (c)

    {

    case 'a':
        printf("0");

        break;

    case 'b':
        printf("1");

        break;

    case 'c':
        printf("2");

        break;

    case 'd':
        printf("3");

        break;

    case 'e':
        printf("4");

        break;

    case 'f':
        printf("5");

        break;

    case 'g':
        printf("6");

        break;

    case 'h':
        printf("7");

        break;

    case 'm':
        printf("8");

        break;

    case 'j':
        printf("9");

        break;

    case 'k':
        printf("10");

        break;

    case 'l':
        printf("11");

        break;

    default:
        printf("%c", t[r]);

        break;
    }
}