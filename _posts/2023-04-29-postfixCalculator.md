---
title: "후위 표현식 계산기"
date: 2022-04-29 01:23:00 +0900
categories: [Algorithm]
tags: [stack,C]
---
# 1. 후위표현식 계산기 소스 코드
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <setjmp.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define MAX_SIZE 100
int stack_oprand[MAX_SIZE];
top_oprand = -1;
char stack[MAX_SIZE];
top = -1;

int is_empty()
{
	if (top == -1) {
		return 1;
	}
	else
		return 0;
}
int is_full()
{
	if (top == MAX_SIZE - 1) {
		return 1;
	}
	else
		return 0;
}
void push(char val) {
	if (!is_full())
	{
		stack[++top] = val;
		if (val + '0' >= '0' && val + '0' <= '9')
			printf("%d가 스택에 푸쉬되었습니다(top : %d)1.\n", val, top);
		else
			printf("%c가 스택에 푸쉬되었습니다(top : %d)2.\n", val, top);
	}
	else
	{
		printf("STACK IS FULL\n");
	}	
}
char pop() {
	char tmp;
	if (!is_empty()) {
		tmp = stack[top--];
		if (tmp + '0' >= '0' && tmp + '0' <= '9')
			printf("%d가 스택에 팝되었습니다(top : %d)1.\n", tmp, top);
		else
			printf("%c가 스택에 팝되었습니다(top : %d)2.\n", tmp, top);
		return tmp;
	}
	else {
		printf("STACK IS EMPTY\n");
	}
}
int is_empty_oprand()
{
	if (top_oprand == -1) {
		return 1;
	}
	else
		return 0;
}
int is_full_oprand()
{
	if (top_oprand == MAX_SIZE - 1) {
		return 1;
	}
	else
		return 0;
}
void push_oprand(int val) {
	if (!is_full_oprand())
	{
		stack_oprand[++top_oprand] = val;
		printf("%d값이 푸쉬되었습니다.(top : %d)\n", val,top_oprand);
	}
	else
	{
		printf("STACK IS FULL\n");
	}
}
int pop_oprand() {
	int tmp;
	if (!is_empty_oprand()) {
		tmp = stack_oprand[top_oprand--];
		printf("%d값이 팝되었습니다.(top : %d)\n", tmp, top_oprand);
		return tmp;
	}
	else {
		printf("STACK IS EMPTY\n");
	}
}
char peek() {
	char tmp;
	if (!is_empty()) {
		
		tmp = stack[top];
		return tmp;
	}
	else {
		printf("STACK IS EMPTY\n");
	}
}
int pie(char ch)
{
	if (ch == '^')
		return 3;
	else if (ch == '*' || ch == '/')
		return 2;
	else if (ch == '+' || ch == '-')
		return 1;
	else if (ch == '(' || ch == '{' || ch == '[' || ch == ')' || ch == '}' || ch == ']')
		return 0;
	else
	{
		printf("WRONG OPERATOR");
		exit(EXIT_FAILURE);
	}
}
char* itoaSub1(int num) { //숫자 to 문자열 while사용 역순출력
	int sum = num;
	int cnt = 0;
	char tmp[MAX_SIZE] = { "" };
	while (sum != 0) {
		sum /= 10;
		cnt++;
	}
	tmp[cnt] = '\0';
	do
	{
		cnt--;
		tmp[cnt] = (char)(num % 10 + 48);
		num = num / 10;
	} while (num != 0);
	printf("tmp : %s\n", tmp);
	return tmp;
}
char* itoaSub2(int num) { //숫자 to 문자열 for사용 순방향출력
	int sum = num;
	int cnt = 0;
	printf("나눌값 : %d\n", sum);
	char tmp[MAX_SIZE] = { "" };
	while (sum != 0) {
		sum /= 10;
		
		cnt++;
	}
	tmp[cnt] = '\0';
	
	for(int i= 0; i<cnt; i++)
	{
		tmp[i] = (char)(num % 10 + 48);
		num = num / 10;
	}
	printf("tmp : %s\n", tmp);
	return tmp;
}
int atoiSub(char* str) { //문자열 to 숫자
	int tmp = 0;
	while (*str) {
		tmp = tmp * 10 + (int)(*str - 48);
		str++;
	}
	return tmp;
}
char* get_exp(char* op_ch) // 사용자로부터 식을 읽어들여서 반환
{
	int i=0;
	static char exp[MAX_SIZE] = { "" };
	sprintf(exp + strlen(exp), "%s", op_ch);

	return exp;
		
}
char get_token(char* exp)
{
	static int idx = 0;
	char token;
	char ch;
	while ((ch = exp[idx++]) == ' ')
	{
		printf("%c\n",ch);
	} 
	token = exp[idx-1];
	return token;
}
char* convertInfixToPostfix(char* exp, char* postfix)
{
	char ch, open_ch;
	char tmp[MAX_SIZE] = { "" };
	char* str;
	int i = 0;
	int check = 0;
	int sum = 0;
	int de = 1;
	while ((ch = get_token(exp)) != '\0') {
		if (ch >= '0' && ch <= '9')
		{
			sprintf(tmp + strlen(tmp), "%c", ch);
			check = 1;
		}
		/*
		if (ch >= '0' && ch <= '9')
		{
			sum += (ch-'0') * de;
			printf("지금까지 더한값 : %d\n", sum);
			de *= 10;
			check = 1;
		}
		*/
		else
		{
			/*
			if (check == 1) //현재 sum값이 존재한다면
			{
				str = itoaSub1(sum);
				sprintf(postfix + strlen(postfix), "%s ", str);
				sum = 0;
				de = 1;
				check = 0;
			}
			*/
			if (check == 1) //현재 sum값이 존재한다면
			{
				sprintf(postfix + strlen(postfix), "%s ", tmp);
				strcpy(tmp, ""); // 문자열 초기화
				check = 0;
			}
			if (ch == '[') // '[' 일 경우
			{
				push(ch); // 스택에 추가
			}
			else if (ch == ']') // ']' 일 경우
			{
				while ((open_ch = pop()) != '[') // '['괄호가 나올 때 까지 항목에 추가
				{
					sprintf(postfix + strlen(postfix), "%c", open_ch);
				}
			}
			else if (ch == '{') // '{' 일 경우
			{
				push(ch); // 스택에 추가
			}
			else if (ch == '}') // '}' 일 경우
			{
				while ((open_ch = pop()) != '{') // '{'괄호가 나올 때 까지 항목에 추가
				{
					sprintf(postfix + strlen(postfix), "%c", open_ch);
				}
				}
			else if (ch == '(') // '(' 일 경우
			{
				push(ch); // 스택에 추가
			}
			else if (ch == ')') // ')' 일 경우
			{
				while ((open_ch = pop()) != '(') // '('괄호가 나올 때 까지 항목에 추가
				{
					sprintf(postfix + strlen(postfix), "%c", open_ch);
				}
			}
			else // 연산자 일 경우
			{
				while (top != -1 && pie(ch) <= pie(peek())) // ch와 스택에 있는 연산자 비교
				{
					sprintf(postfix + strlen(postfix), "%c", pop());
				}
				push(ch); // ch 스택에 추가
			}
		}
	}
	if (check == 1)
	{
		sprintf(postfix + strlen(postfix), "%s ", tmp);
		check = 0;
	}
	while (top != -1) {
		sprintf(postfix + strlen(postfix), "%c", pop());
	}
	return postfix;
}
int eval(char* pexp) {
	int i = 0, val;
	char tmp[MAX_SIZE] = { "" };
	int tmp_exp;
	int sum = 0;
	int de = 1;
	while (pexp[i] != '\0') {
		char ch = pexp[i];
		if (ch == ' ')
		{
			sum = atoiSub(tmp);
			strcpy(tmp, ""); // 문자열 초기화
			push_oprand(sum);
		}
		else if (isdigit(ch)) {
			sprintf(tmp + strlen(tmp), "%c", ch);
		}
		else {
			int op2 = pop_oprand();
			int op1 = pop_oprand();
			printf("op1 : %d, op2 : %d, ch : %c\n", op1, op2,ch);
			switch (ch) {
			case '+':
				val = op1 + op2;
				break;
			case  '-':
				val = op1 - op2;
				break;
			case '*':
				val = op1 * op2;
				break;
			case '/':
				val = op1 / op2;
				break;
			case '^':
				tmp_exp = op1;
				for (int iter = 1; iter < op2; iter++)
				{
					op1 *= tmp_exp;
					printf("제곱op1 : %d\n", op1);
				}
				val = op1;
				break;
			default:
				fprintf(stderr, "잘못된 연산자입니다.\n");
				exit(1);
			}
			printf("val : %d\n", val);
			push_oprand(val);
		}
		i++;
	}
	return pop_oprand();
}
int main() {
	//1 + [{(3*3) + 4/2} + 8-(6/2)*2}] = 8
	//3*(2+8)/5 = 6
	int result=-1;
	char* infix_exp;
	char postfix_exp[MAX_SIZE] = {""};
	char* postfix;

	printf("중위식을 입력하시오.\n");
	char c[MAX_SIZE] = {""};
	scanf("%s", c);
	infix_exp = get_exp(c);

	postfix = convertInfixToPostfix(infix_exp, postfix_exp);
	printf("postfix : %s\n", postfix);
	result = eval(postfix);
	printf("result : %d", result);
}
/*
버그 다 수정
char는 -127 ~ 128까지의 정수 표현이 가능해 int 타입의 새 스택을 이용하지
않을 시 더 높은 자릿수의 연산이 불가능하다.
십진법과 공백을 이용해 2자리 수 이상의 연산이 가능하도록 만들었다.
*/
```