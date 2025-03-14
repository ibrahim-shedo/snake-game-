#include <iostream>
#include <windows.h>
#include <conio.h>
#include <time.h>
#include <vector>
#include <fstream>
#include <algorithm>

using namespace std;

// Game variables
int score, highscore = 0, speed;
vector<int> leaderboard;
bool shieldActive = false;
int shieldDuration = 0;
int powerUpX, powerUpY;
bool powerUpExists = false;
int timeLimit = 60; // Time limit for Time Attack mode (in seconds)
bool timeAttackMode = false; // Flag to check if Time Attack mode is active

// Console settings
HANDLE console = GetStdHandle(STD_OUTPUT_HANDLE);
COORD CursorPosition;

void gotoxy(int x, int y) {
    CursorPosition.X = x;
    CursorPosition.Y = y;
    SetConsoleCursorPosition(console, CursorPosition);
}

void setColor(int color) {
    SetConsoleTextAttribute(console, color);
}

void loadLeaderboard() {
    leaderboard.clear();
    ifstream file("leaderboard.txt");
    if (!file) {
        ofstream createFile("leaderboard.txt");
        createFile.close();
    }
    int temp;
    while (file >> temp) {
        leaderboard.push_back(temp);
    }
    file.close();
}

void saveLeaderboard() {
    leaderboard.push_back(score);
    sort(leaderboard.rbegin(), leaderboard.rend());
    if (leaderboard.size() > 5) leaderboard.resize(5);
    ofstream file("leaderboard.txt");
    for (int s : leaderboard) {
        file << s << endl;
    }
    file.close();
}

void displayTopScorers() {
    loadLeaderboard(); // Ensure the leaderboard is loaded
    cout << "Top 5 Scorers:" << endl;
    for (size_t i = 0; i < leaderboard.size() && i < 5; ++i) {
        cout << i + 1 << ". " << leaderboard[i] << endl;
    }
}

void showInstructions() {
    cout << "Welcome to the Snake Game!" << endl;
    cout << "Controls:" << endl;
    cout << "  W - Move Up" << endl;
    cout << "  S - Move Down" << endl;
    cout << "  A - Move Left" << endl;
    cout << "  D - Move Right" << endl;
    cout << "  P - Pause" << endl;
    cout << "  R - Resume" << endl;
    cout << "Objective: Eat the food (o) to grow and avoid obstacles (X) and walls." << endl;
    cout << " 'P'	Poison (decreases snake length and score)." << endl;
    cout << " '$'	Power-up (increases score, speeds up the game, and activates a shield)." << endl;
    cout<< "Classic Mode: No time limit; the game continues until the snake hits a wall or obstacle."<<endl;
    cout<<"Time Attack Mode: The player must score as many points as possible within a 60-second time limit"<<endl;
    cout << "Press any key to start the game..." << endl;

    getch();
    system("cls");
}

// Function to display the board score and timer
void displayBoardScore(int timeLeft) {
    gotoxy(0, 21); // Position the score below the game grid
    cout << "Score: " << score << "  |  High Score: " << highscore;
    if (shieldActive) {
        cout << "  |  Shield: Active (" << shieldDuration << "s)";
    } else {
        cout << "  |  Shield: Inactive";
    }
    if (timeAttackMode) {
        cout << "  |  Time Left: " << timeLeft << "s";
    }
    cout << endl;
}

int main() {
    loadLeaderboard();
    showInstructions();
    char retry = 'y';
    while (retry == 'y' || retry == 'Y') {
        srand(time(NULL));
        score = 0;

        // Game mode selection
        char mode;
        cout << "Select mode (C - Classic, T - Time Attack): ";
        cin >> mode;
        timeAttackMode = (mode == 'T' || mode == 't');

        // Difficulty selection
        char difficulty;
        cout << "Select difficulty (E - Easy, M - Medium, H - Hard): ";
        cin >> difficulty;
        if (difficulty == 'E' || difficulty == 'e') speed = 150;
        else if (difficulty == 'M' || difficulty == 'm') speed = 100;
        else speed = 75; // Hard mode

        int foodX = rand() % 18 + 1;
        int foodY = rand() % 18 + 1;
        int poisonX = rand() % 18 + 1;
        int poisonY = rand() % 18 + 1;
        powerUpX = rand() % 18 + 1;
        powerUpY = rand() % 18 + 1;
        powerUpExists = true;

        int x[100], y[100];
        x[0] = 10; y[0] = 10;
        int snakeLength = 3;
        char direction = 'd';

        vector<pair<int, int>> obstacles;
        for (int i = 0; i < 5; i++) {
            int obsX = rand() % 18 + 1;
            int obsY = rand() % 18 + 1;
            obstacles.push_back({obsX, obsY});
        }

        bool paused = false;
        int timeLeft = timeLimit; // Initialize timer for Time Attack mode
        clock_t startTime = clock(); // Track the start time

        while (true) {
            // Calculate elapsed time
            if (timeAttackMode) {
                clock_t currentTime = clock();
                int elapsedSeconds = (currentTime - startTime) / CLOCKS_PER_SEC;
                timeLeft = timeLimit - elapsedSeconds;
                if (timeLeft <= 0) {
                    gotoxy(0, 22);
                    cout << "\nTIME'S UP! Score = " << score << endl;
                    saveLeaderboard();
                    displayTopScorers(); // Display top 5 scorers
                    cout << "Retry? (y/n): ";
                    cin >> retry;
                    break;
                }
            }

            string grid[20][20];
            for (int i = 0; i < 20; i++) {
                for (int j = 0; j < 20; j++) {
                    grid[i][j] = (i == 0 || j == 0 || i == 19 || j == 19) ? "# " : "  ";
                }
            }

            grid[foodY][foodX] = "o ";
            grid[poisonY][poisonX] = "P ";
            if (powerUpExists) grid[powerUpY][powerUpX] = "$ ";
            for (auto obs : obstacles) grid[obs.second][obs.first] = "X ";
            for (int i = snakeLength; i > 0; i--) {
                x[i] = x[i - 1];
                y[i] = y[i - 1];
            }
            for (int i = 0; i < snakeLength; i++) grid[y[i]][x[i]] = "* ";

            if (kbhit()) {
                char key = getch();
                if ((key == 'w' && direction != 's') || (key == 's' && direction != 'w') ||
                    (key == 'a' && direction != 'd') || (key == 'd' && direction != 'a')) direction = key;
                if (key == 'p') paused = true;
                if (key == 'r') paused = false;
            }

            while (paused) {
                gotoxy(0, 22);
                cout << "PAUSED - Press 'r' to resume ";
                if (kbhit() && getch() == 'r') paused = false;
                Sleep(100);
            }

            if (direction == 'w') y[0]--;
            if (direction == 's') y[0]++;
            if (direction == 'a') x[0]--;
            if (direction == 'd') x[0]++;

            bool hitWall = (x[0] == 0 || x[0] == 19 || y[0] == 0 || y[0] == 19);
            bool hitObstacle = false;
            for (auto obs : obstacles) {
                if (x[0] == obs.first && y[0] == obs.second) {
                    hitObstacle = true;
                    break;
                }
            }

            gotoxy(0, 0);
            for (int i = 0; i < 20; i++) {
                for (int j = 0; j < 20; j++) {
                    if (hitWall && (i == 0 || j == 0 || i == 19 || j == 19)) {
                        setColor(12); // Red color for the wall
                    } else {
                        setColor(7); // Default color
                    }
                    cout << grid[i][j];
                }
                cout << endl;
            }

            // Update high score
            if (score > highscore) {
                highscore = score;
            }

            // Display the board score and timer
            displayBoardScore(timeLeft);

            if (x[0] == foodX && y[0] == foodY) {
                snakeLength++;
                score++;
                foodX = rand() % 18 + 1;
                foodY = rand() % 18 + 1;
                while (grid[foodY][foodX] != "  ") {
                    foodX = rand() % 18 + 1;
                    foodY = rand() % 18 + 1;
                }
                if (speed > 50) speed -= 5;
            }

            if (x[0] == poisonX && y[0] == poisonY) {
                snakeLength--;
                score--;
                poisonX = rand() % 18 + 1;
                poisonY = rand() % 18 + 1;
                while (grid[poisonY][poisonX] != "  ") {
                    poisonX = rand() % 18 + 1;
                    poisonY = rand() % 18 + 1;
                }
            }

            if (x[0] == powerUpX && y[0] == powerUpY) {
                score += 3;
                speed -= 10;
                shieldActive = true;
                shieldDuration = 5;
                powerUpExists = false;
            }

            if (shieldDuration > 0) shieldDuration--;
            else shieldActive = false;

            if (hitWall || hitObstacle) {
                gotoxy(0, 22);
                cout << "\nGAME OVER! Score = " << score << endl;
                saveLeaderboard();
                displayTopScorers(); // Display top 5 scorers
                cout << "Retry? (y/n): ";
                cin >> retry;
                break;
            }
            Sleep(speed);
        }
    }
    return 0;
}
