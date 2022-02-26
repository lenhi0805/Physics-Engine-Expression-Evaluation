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


