#include <iostream>
#include <fstream>
#include <windows.h>
#include "apmatrix.h"

using namespace std;

//initializing handles
HANDLE hWnd = GetStdHandle(STD_OUTPUT_HANDLE);
HANDLE hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

//prototyping functions
bool mazeFile(char filename[], apmatrix<char> &val, int s[], int &rows, int &cols);
bool findPath(int x, int y, apmatrix<char> &val, int speed);

int main(){
    //declaring variables
    int rows, cols;
    int s[2];
    int speed = 5;
    apmatrix<char> val(1,1,0);
    char filename[10];

    //prompting user and receiving file name and solving speed
    cout << "Enter name of text file containing maze layout (include \".txt\"): ";
    cin >> filename;
    cout << "\nEnter desired speed of maze-solving from 1-10 (10 being the fastest)";
    cout << "\n**NOTE 1 is good for small mazes, 10 for enormous ones, etc**: ";
    cin >> speed;

    //adjusting speed
    if(speed < 11 && speed > 0){
        speed -= 10;
        speed = abs(speed);
        speed *= 10;
        speed += 1;
    }

    //checking to see if maze file provided actually exists
    if (mazeFile(filename, val, s, rows, cols)) {
        //setting start position to the S in maze file
        int x = s[1];
        int y = s[0];

        //beginning recursion
        if(findPath(x, y, val, speed)) {
            //changing program conclusion back to white
            SetConsoleTextAttribute(hConsole, 15);

            //initializing coordinates for new cursor position
            COORD coord;
            coord.X = 0;
            coord.Y = val.numrows();

            //setting cursor position to new coordinates
            SetConsoleCursorPosition(hWnd, coord);

            cout << "\n\n";
        } else {
            //changing program conclusion back to white
            SetConsoleTextAttribute(hConsole, 15);

            //initializing coordinates for new cursor position
            COORD coord;
            coord.X = 0;
            coord.Y = val.numrows();

            //setting cursor position to new coordinates
            SetConsoleCursorPosition(hWnd, coord);

            //outputting error message if maze is not solvable
            cout << "\n\nThis maze is unsolvable!" << endl;
        }
    }

    return 0;
}

//function to receive data from maze file
bool mazeFile(char filename[], apmatrix<char> &val, int s[], int &rows, int &cols){
    //declaring variables
    bool g = false;

    //opening maze file
    ifstream datafile(filename);

    //error message if file could not be found
    if(!datafile) {
        cerr << "Maze file could not be located.";

        return 0;
    }
    else {
        //clearing prompts
        system("CLS");

        //reading in predetermined dimensions of maze
        datafile >> rows;
        datafile >> cols;

        //setting start coordinates to a temporary impossible value
        s[0] = rows + 1;
        s[1] = cols + 1;

        //declaring variable to receive each row of data from file
        char line[cols];

        //resizing data matrix to fit new dimensions
        val.resize(rows, cols);

        //going through each row
        for (int a = 0; a < rows; a++) {
            //reading in the current row as a single char array
            datafile >> line;

            //going through each character of the row
            for (int b = 0; b < cols; b++) {
                //putting current character into the data matrix
                val[a][b] = line[b];

                //setting starting coordinates to start, when found
                if(val[a][b] == 'S') {
                    s[0] = a;
                    s[1] = b;

                    //printing start in pink
                    SetConsoleTextAttribute(hConsole, 13);
                    cout << val[a][b] << " ";
                } else if(val[a][b] == 'G') {
                    //if goal is found, adjust the boolean that says so
                    g = true;

                    //printing goal in pink
                    SetConsoleTextAttribute(hConsole, 13);
                    cout << val[a][b] << " ";
                } else {
                    //if current character is neither start nor goal, print it
                    SetConsoleTextAttribute(hConsole, 15);
                    cout << val[a][b] << " ";
                }

                //setting text color back to white
                SetConsoleTextAttribute(hConsole, 15);
            }

            //moving onto the next row
            cout << endl;
        }

        //if there is either no start or no goal
        if (s[0] + s[1] == rows + cols + 2 || g == false){
            //output error
            cerr << "\n\nFile lacks either a start or a goal.";

            return 0;
        } else {
            return 1;
        }
    }
}

//recursive function to determine path from start to goal using depth-first-search algorithm
bool findPath(int x, int y, apmatrix<char> &val, int speed){
    //return false if maze pixel is outside of the map, a wall, or already visited
    if(x < 0
    || x > val.numcols() - 1
    || y < 0
    || y > val.numrows() - 1
    || val[y][x] == '#'
    || val[y][x] == '+'
    || val[y][x] == 'x') {
        return 0;
    } else if(val[y][x] == 'G'){
        //return true if goal is found
        return 1;
    } else {
        //if character is empty space
        if(val[y][x] != 'S'){
            //pause for a user entered interval
            Sleep(speed);

            //marking character as part of path
            val[y][x] = '+';

            //setting cursor position to appropriate pixel
            COORD coord;
            coord.X = 2*x;
            coord.Y = y;

            //coloring text green
            SetConsoleTextAttribute(hConsole, 10);

            //printing new character in the spot of the old character
            SetConsoleCursorPosition(hWnd, coord);
            cout << val[y][x];
        }
    }

    //beginning recursion to follow each direction from every new pixel

    //north
    if(findPath(x, y - 1, val, speed)){
        return 1;
    }

    //east
    if(findPath(x + 1, y, val, speed)){
        return 1;
    }

    //south
    if(findPath(x, y + 1, val, speed)){
        return 1;
    }

    //west
    if(findPath(x - 1, y, val, speed)){
        return 1;
    }

    //if all paths are blocked

    //if pixel is not the start, return false to the previous recursive branch
    if(val[y][x] != 'S'){
        //mark pixel not part of path
        val[y][x] = 'x';

        //setting cursor to appropriate coordinates
        COORD coord;
        coord.X = 2*x;
        coord.Y = y;

        //setting text color to red
        SetConsoleTextAttribute(hConsole, 12);

        //printing new character
        SetConsoleCursorPosition(hWnd, coord);
        cout << val[y][x];
    }

    return 0;
}
