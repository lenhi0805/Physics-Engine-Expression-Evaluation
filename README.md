# Physics-Engine-Expression-Evaluation
. You have been assigned the task to create a program that can evaluate polynomial expressions used by the engine.
//Nhi Le

#include <iostream>
#include <fstream>
#include <string>
#include <cmath>
#include "Node.h"
#include "LinkedList.h"
using namespace std;

// find x value in each line 
string findX(string &line, long unsigned int &start){
  string res;
  for (; start < line.length(); start++){ 
      if (line[start] != ')'){ // read x until it reaches ")"
        res += line[start];
      } else{
        start += 3; 
        break;
      }
  }
  return res;
}
// find coefficient and expression values 
 void findNode(LinkedList &list, long unsigned int &start, string &line){
  string coef = "";
  string expr = "";
  for (; start < line.length(); start++){
    //divided by 2 cases, if the coef length is already 2, the function will break, if not it will continue. 
    if (line[start] == ' ' && coef.length() < 2){ 
            continue;
    } else if (line[start] == ' ' && coef.length() >= 2){
      break;
    }
    if (line[start] != 'x' || line[start] == '-' || line[start] == '+'){
        coef += line[start];
    } else{
        break;
    }
  }
  if (coef == "+" || coef == ""){  // if there is no "^" after x, the expression will be -1 or +1                                     depend on the sign. 
        coef = "1";
    } else if (coef == "-"){
        coef = "-1";
    }

  if (line[start] != 'x'){ // in case there is no x, the expression value will be 0. 
        expr = '0';
        start++;
    } else {
        start++;
        if (line[start] != '^'){
            expr = '1';
        } else {
          // check if the next character will be space or the exponent symbol
            for (; start < line.length();  start++){ 
                if (line[start] == ' '){
                    break;
                } else if (start == '^'){
                    continue;
                }
                if (isdigit(line[start]) || line[start] == '-'){
                    expr += line[start];
                }
            }
        }
    }
  //cout << coef << "x^" << expr << " ";
  // if the same expression value existed , it will automatically calculate the total value 
  bool existed = false;
  for (int i = 0; i < list.getLength(); i++){
      if (list[i]->getExp() == stoi(expr)){
          list[i]->setCoef(list[i]->getCoef() + stod(coef));
          existed = true;
          break;
      }
  }
  //if not existed, it will create a new node
  if (!existed){
      Node *newNode = new Node(stod(coef), stoi(expr));
      list += newNode;
  }
}

int main() {
  string filename;
	ifstream file;
	cout << "Please enter file name: ";
  cin >> filename;
	file.open(filename);
	if (file){
    string line;
    //read each line 
    while(getline(file, line)){
      long unsigned int start = 2; // the x value is in position 2, so start=2  
      string xVal = findX(line, start);
      //cout << xVal <<" "<< start << endl;
      //long unsigned int start = 6 + xVal.length();
      LinkedList llist;
      while (start < line.length()){
        findNode(llist, start, line);
        //cout << start << endl;
      }
      double x = stod(xVal);
        llist.sortList();
        cout << llist;
        double sum = 0;
        for (int i = 0; i < llist.getLength(); i++){
            sum += llist[i]->getCoef()*pow(x, 1.0*llist[i]->getExp());
        }
        printf("= %.3f", sum); // print  3 decimals
        cout << endl;
    }
  }
} 

	// LinkList.cpp
#include "LinkedList.h"
#include <iostream>
//default constructor
LinkedList::LinkedList(){
  this->length = 0;
  this->head = nullptr;
}

int LinkedList::getLength(){
  return this->length;
}
Node*& LinkedList::getHead(){
  return this->head;
}

void LinkedList::setLength(int len){
  this->length = len;
}

void LinkedList::setHead(Node* node){
  if (node == nullptr) {
        this->head = nullptr;
    }
    else {
        this->head = new Node(node->getCoef(), node->getExp());
        this->length++;
        Node *curr = this->head;
        Node *nodeHead = node;
        Node *currNode = nodeHead;
        while (currNode->getNext() != nullptr) {
            curr->setNext(new Node(currNode->getNext()->getCoef(), currNode->getNext()->getExp()));
            this->length++;
            currNode = currNode->getNext();
            curr = curr->getNext();
        }
    }
}

void LinkedList::addNode(Node*&node){
    Node* add = new Node(node->getCoef(), node->getExp());
    if (this->head == nullptr){
        this->head = add;
        this->length ++;
    } else {
        Node* curr = this->head;
        while (curr->getNext() != nullptr){
            curr = curr->getNext();
        }
        curr->setNext(add);
        this->length ++;
    }
}


  void LinkedList::display() {
    Node* curr = this->head;
    while (curr != nullptr){
        if (curr->getExp() == 0){
            std::cout << curr->getCoef() << " " << " ";
        } else if (curr->getExp() == 1){
            std::cout << curr->getCoef() << "x" << " ";
        }else {
            std::cout << curr->getCoef() << "x^" << curr->getExp() << " " << " ";
        }
        curr = curr->getNext();
    }
  }
// selection sort
  void sortExp(Node* &head)
{
    Node *ptr1, *ptr2, *ptr3, *ptr4, *ptr5;
 
    ptr1 = ptr2 = head;
 
    while (ptr2->getNext()) {
 
        ptr3 = ptr4 = ptr2->getNext();

        while (ptr4) {
 
            if (ptr2->getExp() < ptr4->getExp()) {

                if (ptr2->getNext() == ptr4) {
 
                   
                    if (ptr2 == head) {
 
                        ptr2->setNext(ptr4->getNext());
                        ptr4->setNext(ptr2);
 
                        
                        ptr5 = ptr2;
                        ptr2 = ptr4;
                        ptr4 = ptr5;
 
                        ptr3 = ptr4;
 
                        head = ptr2;                        
                        ptr4 = ptr4->getNext();
                    }
 
                    
                    else {
 
                        
                        ptr2->setNext(ptr4->getNext());
                        ptr4->setNext(ptr2);
                        ptr1->setNext(ptr4);
 
                       
                        ptr5 = ptr2;
                        ptr2 = ptr4;
                        ptr4 = ptr5;
                        ptr3 = ptr4;

                        ptr4 = ptr4->getNext();
                    }
                }
 
  
                else {
 
                  
                    if (ptr2 == head) {
 
                        
                        ptr5 = ptr2->getNext();
                        ptr2->setNext(ptr4->getNext());
                        ptr4->setNext(ptr5);
                        ptr3->setNext(ptr2);
 
                        ptr5 = ptr2;
                        ptr2 = ptr4;
                        ptr4 = ptr5;
 
                        ptr3 = ptr4;
 
                        ptr4 = ptr4->getNext();
 
                        head = ptr2;
                    }
 
                   
                    else {
 
                        
                        ptr5 = ptr2->getNext();
                        ptr2->setNext(ptr4->getNext());
                        ptr4->setNext(ptr5);
                        ptr3->setNext(ptr2);
                        ptr1->setNext(ptr4);
 
                        
                        ptr5 = ptr2;
                        ptr2 = ptr4;
                        ptr4 = ptr5;
 
                        ptr3 = ptr4;
 
                        ptr4 = ptr4->getNext();
                    }
                }
            }
            else {

                ptr3 = ptr4;
                ptr4 = ptr4->getNext();
            }
        }
 
        ptr1 = ptr2;
        ptr2 = ptr2->getNext();
    }
}
//std::ostream& operator<<(std::ostream& os,const LinkedList D){
  
//}
// create linked list
Node* LinkedList::operator[](int index) {
  Node *ptr= head;
  for (int x=0;x<index;x++){
    ptr=ptr->getNext();
  } 
  return ptr;
 }
 std::ostream& operator<<(std::ostream& os, LinkedList &list){
   for (int x = 0; x < list.getLength(); x++){
      if (list[x]->getCoef() == 0){
        continue;
      }
        if (x == 0){
            os << *list[x] << " ";
        } else if (list[x]->getCoef() > 0){
            os << "+ " << *list[x] << " ";
        } else {
            if (list[x]->getExp() == 0){
                os << "- " << (-1)*(list[x]->getCoef()) << " ";
            } else if (list[x]->getExp() == 1){
                os << "- " << (-1)*(list[x]->getCoef()) << "x" << " ";
            } else{
                os << "- " << (-1)*(list[x]->getCoef()) << "x^" << list[x]->getExp() << " ";
            }
        }
    }
    return os;
 }
 LinkedList& LinkedList::operator+=(Node* &ptr){
   Node* add = new Node(ptr->getCoef(), ptr->getExp());
    if (this->head == nullptr){
        this->head = add;
        this->length ++;
    } else {
        Node* curr = this->head;
        while (curr->getNext() != nullptr){
            curr = curr->getNext();
        }
        curr->setNext(add);
        this->length ++;
    }
return *this;
 }
  void LinkedList::sortList(){
  sortExp(this->head);
}
	
	//LinkList.h
	#ifndef LLIST_H  //if not defined
#define LLIST_H
#include "Node.h"

class LinkedList {
private:
  int length;
  Node *head;
public :
  //default constructor
  LinkedList ();
  //overloaded constructor
  LinkedList(LinkedList&);
  //destructor
  //~LinkedList();
  //getters
  int getLength();

  Node*& getHead();
  //setters
  void setLength(int);
  void setHead(Node*);

  void addNode(Node*&);
  void display();
  void sortList();
  Node* operator[](int index);
  LinkedList& operator+=(Node*&ptr);
  friend std::ostream& operator<<(std::ostream& os, LinkedList&);
};
#endif
	//node.cpp
#include "Node.h"
#include <iostream>
#include <iomanip>
Node::Node(){
  this->coef = 0;
  this->exp = 0;
  this->next = nullptr;
}

Node::Node (double coef,int exp){
  this -> coef= coef;
  this -> exp = exp;
  this -> next = nullptr; 
}

//getters
double Node::getCoef() const{
  return this->coef;
}
int Node::getExp() const{
  return this->exp;
}
Node* Node::getNext(){
    return this->next;
}

//setters
void Node::setCoef(double val){
  this->coef = val;
}
void Node::setExp(int val){
  this->exp = val;
}
void Node::setNext(Node * node){
  this->next = node;
}

std::ostream& operator<<(std::ostream& os, Node &ptr)
{ if (ptr.exp==0) {
  os << std::fixed << std::setprecision(3) << ptr.coef;
     return os; }
  if (ptr.exp==1){
    if (ptr.coef == 1){
      os << "x";
    } else{
     os << std::fixed << std::setprecision(3) << ptr.coef<<"x";
    }
     return os;}
  else { 
    if (ptr.coef == 1){
            os << "x^" << ptr.exp;
        } else{
            os << std::fixed << std::setprecision(3) << ptr.coef << "x^" << ptr.exp;
        }
  }
  return os;
  }
  
std::ostream& operator<<(std::ostream& os, const Node &ptr)
{ if (ptr.exp==0) {
  os <<ptr.coef;
     return os; }
  if (ptr.exp==1){
     os <<ptr.coef<<"x";
     return os;}
  else { os <<ptr.coef<<"x^"<<ptr.exp;
     return os;}

  }
std::ostream& operator<<(std::ostream& os, Node* &ptr)
{ if (ptr->exp==0) {
  os <<ptr->coef;
     return os; }
  if (ptr->exp==1){
     os <<ptr->coef<<"x";
     return os;}
  else { os <<ptr->coef<<"x^"<<ptr->exp;
     return os;}

  }
	
	//Node.h
	
#ifndef NODE_H   //if not defined
#define NODE_H
#include <iostream>
class Node {
private:
  double coef;
  int exp;
  Node *next;
public:
  //default constructor
  Node();
  //overloaded constructor
  Node (double,int);
  //destructor
  //~Node();
  //getters
  double getCoef() const;
  int getExp() const;
  Node* getNext();
  //setters
  void setCoef(double);
  void setExp(int);
  void setNext(Node*);
  
  friend std::ostream& operator<<(std::ostream&, Node&);
  friend std::ostream& operator<<(std::ostream&, Node*&);
  friend std::ostream& operator<<(std::ostream&, const Node&);
};


#endif 

