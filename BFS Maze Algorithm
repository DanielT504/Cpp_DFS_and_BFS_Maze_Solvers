#include <iostream>
#include <fstream>
#include <windows.h>
#include "apmatrix.h"
#include <queue>

using namespace std;

//initializing handles
HANDLE hWnd = GetStdHandle(STD_OUTPUT_HANDLE);
HANDLE hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

//prototyping functions
bool mazeFile(char filename[], apmatrix<char> &val, int s[], int &rows, int &cols, int goal[]);
void findPath(apmatrix<char> &val, bool &g, queue<int> &Y, queue<int> &X, int y, int x, apmatrix<int> &d, int cardinal, int speed);
void drawPath(apmatrix<int> d, apmatrix<char> &val, int goal[], int speed);
void noPath(apmatrix<char> val);

int main(){
    //declaring variables
    int rows, cols;
    int s[2], goal[2];
    int cardinal, speed = 5;
    apmatrix<char> val(1,1,0);
    char filename[10];
    bool g = false;
    queue<int> X, Y;

    //user prompts, and receiving maze file name and solution speed
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

    //checking if specified maze file actually exists
    if (mazeFile(filename, val, s, rows, cols, goal)) {
        //setting first values in x and y queues to the starting coordinates
        Y.push(s[0]);
        X.push(s[1]);

        //creating matrix of directions for each pixel, as needed by the BFS algorithm
        apmatrix<int> d(rows, cols, 0);

        //continue algorithm as long as queue is not empty and the goal has not been found
        while(!Y.empty() && !g){
            //go through each direction at every new pixel

            //north cardinal
            cardinal = 1;
            findPath(val, g, Y, X, Y.front() - 1, X.front(), d, cardinal, speed);
            //east cardinal
            cardinal = 2;
            findPath(val, g, Y, X, Y.front(), X.front() + 1, d, cardinal, speed);
            //south cardinal
            cardinal = 3;
            findPath(val, g, Y, X, Y.front() + 1, X.front(), d, cardinal, speed);
            //west cardinal
            cardinal = 4;
            findPath(val, g, Y, X, Y.front(), X.front() - 1, d, cardinal, speed);

            //eliminate coordinates from queue after each direction from them have been checked
            Y.pop();
            X.pop();
        }

        //if the goal was found
        if(g) {
            //setting text color to green
            SetConsoleTextAttribute(hConsole, 10);

            //function to draw correct path by following arrows back to start
            drawPath(d, val, goal, speed);

            //function to mark the incorrect paths as such
            noPath(val);

            //setting text color back to white
            SetConsoleTextAttribute(hConsole, 15);

            //adjusting cursor position to end of maze
            COORD coord;
            coord.X = 0;
            coord.Y = val.numrows();

            SetConsoleCursorPosition(hWnd, coord);

            cout << "\n\n";
        } else {
            //function to show impossibility of maze
            noPath(val);

            //setting text color back to white
            SetConsoleTextAttribute(hConsole, 15);

            //adjusting cursor position to end of maze
            COORD coord;
            coord.X = 0;
            coord.Y = val.numrows();

            SetConsoleCursorPosition(hWnd, coord);

            //outputting conclusion
            cout << "\n\nThis maze is unsolvable!" << endl;
        }
    }

    return 0;
}

//function to open and retrieve data from provided maze file
bool mazeFile(char filename[], apmatrix<char> &val, int s[], int &rows, int &cols, int goal[]){
    //opening maze file
    ifstream datafile(filename);

    //error message if maze file doesn't exist
    if(!datafile) {
        cerr << "Maze file could not be located.";

        return 0;
    }
    else {
        //clear original prompts
        system("CLS");

        //reading in predetermined maze dimensions
        datafile >> rows;
        datafile >> cols;

        //setting start and goal coordinates to temporary impossible values
        s[0] = rows + 1;
        s[1] = cols + 1;
        goal[0] = rows + 1;
        goal[1] = cols + 1;

        //declaring string to receive each row of maze
        char line[cols];

        //resizing data matrix to fit maze dimensions
        val.resize(rows, cols);

        //going through each row
        for (int a = 0; a < rows; a++) {
            //reading in entire row as a single string
            datafile >> line;

            //for each character of the row
            for (int b = 0; b < cols; b++) {
                //putting current character into data matrix
                val[a][b] = line[b];

                //checking if current character is the start
                if(val[a][b] == 'S') {
                    //if it is, save coordinates
                    s[0] = a;
                    s[1] = b;

                    //printing start of maze in pink
                    SetConsoleTextAttribute(hConsole, 13);
                    cout << val[a][b] << " ";

                    //marking start position as an empty space
                    val[a][b] == '.';
                } else if(val[a][b] == 'G') {
                    //if current character is the goal, save coordinates
                    goal[0] = a;
                    goal[1] = b;

                    //printing goal of maze in pink
                    SetConsoleTextAttribute(hConsole, 13);
                    cout << val[a][b] << " ";
                } else {
                    //if current character is neither start no goal, print it
                    cout << val[a][b] << " ";
                }

                //setting text color back to white
                SetConsoleTextAttribute(hConsole, 15);
            }

            //next row
            cout << endl;
        }

        //if either start or goal still have impossible coordinates, return error message
        if (s[0] + s[1] == rows + cols + 2 || goal[0] + goal[1] == rows + cols + 2){
            cerr << "\n\nFile lacks either a start or a goal.";

            return 0;
        } else {
            return 1;
        }
    }
}

//function to execute breadth-first-search algorithm, but not draw the path yet
void findPath(apmatrix<char> &val, bool &g, queue<int> &Y, queue<int> &X, int y, int x, apmatrix<int> &d, int cardinal, int speed){
    //check if pixel is outside of maze, a wall, or already visited
    if(x < 0
    || x > val.numcols() - 1
    || y < 0
    || y > val.numrows() - 1
    || val[y][x] == '#'
    || val[y][x] == 'v'
    || val[y][x] == '<'
    || val[y][x] == '>'
    || val[y][x] == '^') {
        //do nothing
    } else if(val[y][x] == 'G'){
        //if pixel is the goal

        //determining direction that the current path just came from
        d[y][x] = cardinal;

        //found goal
        g = 1;
    } else {
        //determining direction that the current path just came from
        d[y][x] = cardinal;

        //adding current coordinates to queue
        Y.push(y);
        X.push(x);

        //if current character is not the start
        if(val[y][x] != 'S'){
            //pause for a user-specified interval
            Sleep(speed);

            //set current pixel as arrow  respective to which direction path just came from
            if(cardinal == 1){
                val[y][x] = 'v';
            } else if(cardinal == 2){
                val[y][x] = '<';
            } else if(cardinal == 3){
                val[y][x] = '^';
            } else if(cardinal == 4){
                val[y][x] = '>';
            }

            //changing text color to yellow
            SetConsoleTextAttribute(hConsole, 14);

            //setting cursor position to current coordinates
            COORD coord;
            coord.X = x * 2;
            coord.Y = y;

            //print new character from data matrix
            SetConsoleCursorPosition(hWnd, coord);
            cout << val[y][x];
        }
    }
}

//function to draw correct path back to start
void drawPath(apmatrix<int> d, apmatrix<char> &val, int goal[], int speed){
    //pause for a user-specified interval
    Sleep(speed);

    //proceeding depending on which direction the specific pixel came from

    if(d[goal[0]][goal[1]] == 3) {
        //go north
        goal[0] -= 1;

        //if it hasn't reached the start yet
        if(val[goal[0]][goal[1]] != 'S'){
            //adjusting cursor position to new segment of path
            COORD coord;
            coord.X = goal[1] * 2;
            coord.Y = goal[0];

            //marking pixel as part of correct path
            val[goal[0]][goal[1]] = '+';

            //printing new part of correct path
            SetConsoleCursorPosition(hWnd, coord);
            cout << val[goal[0]][goal[1]];

            //repeats by calling itself until start is reached
            drawPath(d, val, goal, speed);
        }
    } else if(d[goal[0]][goal[1]] == 4) {
        //go east
        goal[1] += 1;

        //if it hasn't reached the start yet
        if(val[goal[0]][goal[1]] != 'S'){
            //adjusting cursor position to new segment of path
            COORD coord;
            coord.X = goal[1] * 2;
            coord.Y = goal[0];

            //marking pixel as part of correct path
            val[goal[0]][goal[1]] = '+';

            //printing new part of correct path
            SetConsoleCursorPosition(hWnd, coord);
            cout << val[goal[0]][goal[1]];

            //repeats by calling itself until start is reached
            drawPath(d, val, goal, speed);
        }
    } else if(d[goal[0]][goal[1]] == 1) {
        //go south
        goal[0] += 1;

        //if it hasn't reached the start yet
        if(val[goal[0]][goal[1]] != 'S'){
            //adjusting cursor position to new segment of path
            COORD coord;
            coord.X = goal[1] * 2;
            coord.Y = goal[0];

            //marking pixel as part of correct path
            val[goal[0]][goal[1]] = '+';

            //printing new part of correct path
            SetConsoleCursorPosition(hWnd, coord);
            cout << val[goal[0]][goal[1]];

            //repeats by calling itself until start is reached
            drawPath(d, val, goal, speed);
        }
    } else if(d[goal[0]][goal[1]] == 2) {
        //go west
        goal[1] -= 1;

        //if it hasn't reached the start yet
        if(val[goal[0]][goal[1]] != 'S'){
            //adjusting cursor position to new segment of path
            COORD coord;
            coord.X = goal[1] * 2;
            coord.Y = goal[0];

            //marking pixel as part of correct path
            val[goal[0]][goal[1]] = '+';

            //printing new part of correct path
            SetConsoleCursorPosition(hWnd, coord);
            cout << val[goal[0]][goal[1]];

            //repeats by calling itself until start is reached
            drawPath(d, val, goal, speed);
        }
    }
}

//function to mark all incorrect maze pixels as such
void noPath(apmatrix<char> val){
    //setting text color to red
    SetConsoleTextAttribute(hConsole, 12);

    //for each row of entire maze
    for(int q = 0; q < val.numrows(); q++){
        //for each character of each row
        for(int r = 0; r < val.numcols(); r++){
            //if pixel is not part of correct path, a wall, or start
            if(val[q][r] == 'v' || val[q][r] == '<' || val[q][r] == '>' || val[q][r] == '^'){
                //set pixel value to incorrect
                val[q][r] = 'x';

                //adjusting cursor position to current coordinates
                COORD coord;
                coord.X = r * 2;
                coord.Y = q;

                //marking pixel as incorrect
                SetConsoleCursorPosition(hWnd, coord);
                cout << val[q][r] << " ";
            }
        }

        cout << endl;
    }
}
