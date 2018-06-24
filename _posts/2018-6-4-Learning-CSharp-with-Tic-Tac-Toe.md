---
layout: post
title: Learning C# with Tic Tac Toe
---

In C# Player's Guide after learning about classes in C#, R.B. Whitaker has a Try-It-Yourself for a Tic-Tac-Toe game to solidify the learning of enums, classes, class variables and methods.

The requirements for this Tic-Tac-Toe game were as follows:
- The game is console-based.
- Two human players take turns using the same keyboard.
- The players are able to designate which square they want to play in next using the keyboard.
- The game should ensure that only legal moves are made.
  - You can't play in a square that already has something.
  - The wrong player can't take a turn.
  - If the player makes an illegal move, the board should remain unchanged.
- The game needs to be able to detect when either players wins, or when a draw occurs.
- Outcome is displayed to user
- Only one round is required


I have had problems in the past creating a Tic Tac Toe project in JavaScript. But, thanks to reviewing resources on [how to think like a programmer](https://medium.freecodecamp.org/how-to-think-like-a-programmer-lessons-in-problem-solving-d1d8bf1de7d2), I found myself thinking about this problem in a vastly different way than I did when trying in JavaScript. I really think the mindset of "Divide and Conquer" was helpful. [This](https://www.youtube.com/watch?v=11V7Ik0IBHU) YouTube video by  V. Anton Spraul explains it in a way that I could grasp. The first step to wrap my mind around this was to carefully read and understand each requirement.

Next was high-level thinking. What kind of classes do you need for Tic Tac Toe? Simply put I figured I'd need a Player class, a Board class, a Renderer class, a Wincheck class, and finally the Engine class to run the game for us. With that basic idea in my had what should each class do? How will they interact? Here's how I had it in my head.

My Player class will have Piece property that can only be "X" or "O" and also stores the player's name. Simple and to the point. Notice that both properties are read-only outside of the class. I believe this is as strict of access as needed. You'll notice that most of my class variables are this way. A point I got from C# Player's Guide is make your variables as strict as possible to make your program function properly.

```csharp
class Player
{
    public string Piece { get; private set; }
    public string Name { get; private set; }


    public Player(string piece, string name)
    {
        this.Name = name;
        // ensure our player has a correct piece for tic tac toe
        if(piece != "X" && piece != "O")
        {
            this.Piece = GetValidPiece();
        }

        this.Piece = piece;
    }

    private string GetValidPiece()
    {
        string userInput = "U";

        while(userInput != "X" || userInput != "O")
        {
            Console.WriteLine("Enter a valid piece (X or O)");
            userInput = Console.ReadLine();
        }

        return userInput;
    }
}
```

For my Board class I want to keep track of the board. I'll also want to set pieces and handle incorrect user input.

```csharp
class Board
{
    public string[] BoardPieces { get; private set; }

    public Board()
    {
        this.BoardPieces = new string[] { "U", "U", "U", "U", "U", "U", "U", "U", "U" };
    }

    public void setPiece(Player player, int position)
    {

        string playerPiece = player.Piece;
        int newPosition = -1;

        //check for piece already at position
        if(BoardPieces[position] == "U")
        {
            BoardPieces[position] = playerPiece;
        }
        else
        {
            newPosition = GetValidPositionFromUser();
            this.setPiece(player, newPosition); // call setPiece function again with a correct position.
        }

    }

    public int GetValidPositionFromUser()
    {
        int validInput = -1;

        while(validInput < 0)
        {
            Console.WriteLine();
            Console.WriteLine("Enter a valid position 1-9:");
            string userInput = Console.ReadLine();
            if (int.TryParse(userInput, out int number) && (number > -1 && number < 9))
            {
                validInput = number - 1; // ensure we go from 1-9 range to 0-8 for proper array indexing
            }
        }

        return validInput;
    }

}
```

The Renderer class will be given a Board and simply render the board. While we could have a method in the board class that renders itself, I think this helps keep our classes simpler.

```csharp
class Renderer
{

    private Board board;

    public Renderer(Board board)
    {
        this.board = board;
    }

    public void DisplayCurrentBoard()
    {
        string[] boardPieces = board.BoardPieces;
        Console.WriteLine($"{boardPieces[0]} | {boardPieces[1]} | {boardPieces[2]}");
        Console.WriteLine("---------");
        Console.WriteLine($"{boardPieces[3]} | {boardPieces[4]} | {boardPieces[5]}");
        Console.WriteLine("---------");
        Console.WriteLine($"{boardPieces[6]} | {boardPieces[7]} | {boardPieces[8]}");
        Console.WriteLine();
        Console.WriteLine();

    }

}
```

The Winchecker class should have a private class variable to store the ways of winning tic-tac-toe. Those ways are: column, row, diagonal. You'll see that these win conditions are stored as a list of integer arrays. These integers correspond with the indexes of Board pieces. In our DidPlayerWin method you'll notice we iterate through each item in the list and check if the player won.

I originally thought of using a 2D array of int type to store the win conditions but, I found the .ForEach() method on the List<> type to be much more readable.

```csharp
class WinCondition
{

    public List<int[]> WinConditions { get; private set; }
    public Board Board { get; private set; }

    public WinCondition(Board board)
    {
        WinConditions = new List<int[]>
        {
            new int[] { 0, 1, 2 },
            new int[] { 3, 4, 5 },
            new int[] { 6, 7, 8 },
            new int[] { 0, 3, 6 },
            new int[] { 1, 4, 7 },
            new int[] { 2, 5, 8 },
            new int[] { 0, 4, 8 },
            new int[] { 2, 4, 6 }
        };

        this.Board = board;
    }

    public bool DidPlayerWin(string playerPiece)
    {
        bool didTheyWin = false;

        WinConditions.ForEach(setOfWinConditions =>
        {

            if(Board.BoardPieces[setOfWinConditions[0]] == playerPiece &&
            Board.BoardPieces[setOfWinConditions[1]] == playerPiece
            && Board.BoardPieces[setOfWinConditions[2]] == playerPiece)
            {
                didTheyWin = true;
            }

        });

        return didTheyWin;
    }
}
```

Finally our class that will run the game for us is the Engine class. This Engine class will ask for the information needed to create two players and will automatically make us a WinCondition class. The parameters for the constructor will be a Board and a Renderer. I believe that I could make the Engine run without needing to have any parameters. I'm not entirely sure why I didn't but we'll discuss that later. We'll also discuss the Save() and Load() methods as this wasn't a requirement and something I added to learn a little about Object serialization. Side note: the best learning experience for me when it comes to Object serialization was serializing and object and deserializing an object in Ruby by reading and writing to a file and parsing the text. [Here's a link](https://github.com/crandonriordan/hangman-ruby) to the GitHub project where this was done.

The Start() method will start the game up and if no player wins after the 9th loop it will decide it's a draw. Our sequence of events for the Start() is
1. "X" player goes first.
2. Check for a valid move.
3. Set piece if the move is valid.
4. Render the board to the player.
5. Check for a win.
6. Toggle the WhoseTurn variable to the other player.
7. Repeat until win or tie.

The PrintColoredMessage() method is just a simple helper function as I thought the grey text looked dreary.

```csharp
class Engine
{
    private Board Board { get; set; }
    public Renderer Renderer { get; private set; }
    public Player PlayerOne { get; private set; }
    public Player PlayerTwo { get; private set; }
    public Player WhoseTurn { get; private set; } // To track whose turn
    public WinCondition Win { get; private set; }


    public Engine(Board board, Renderer renderer)
    {
        this.Board = board;
        this.Renderer = renderer;
        this.Win = new WinCondition(Board);

        Console.WriteLine("Enter the name of the first player (X)");
        string playerOneName = Console.ReadLine();
        this.PlayerOne = new Player("X", playerOneName);

        this.WhoseTurn = PlayerOne;

        Console.WriteLine("Enter the name of the first player (O)");
        string playerTwoName = Console.ReadLine();
        this.PlayerTwo = new Player("O", playerTwoName);
    }

    public void Start()
    {
        PrintColoredMessage(ConsoleColor.Green, "Welcome to v0.1 of Tic-Tac-Toe");

        int numOfTurns = 0;

        while(numOfTurns < 9)
        {
            PrintColoredMessage(ConsoleColor.Red, "If you'd like to save type save");
            string wantsToSave = Console.ReadLine();
            if (wantsToSave.ToLower() == "save")
                Save();

            PrintColoredMessage(ConsoleColor.Red, "If you'd like to load type load");
            string wantsToLoad = Console.ReadLine();
            if (wantsToLoad.ToLower() == "load")
            {
                Engine loadedEngine = Load();
                loadedEngine.Start();

            }


            PrintColoredMessage(ConsoleColor.Cyan, $"{WhoseTurn.Name} is up now!");

            Board.setPiece(WhoseTurn, Board.GetValidPositionFromUser());

            Renderer.DisplayCurrentBoard();

            if(CheckWinner())
            {
                break;
            }

            if((numOfTurns + 1) < 9)
            {
                Console.WriteLine("Shoot, a tie!");
                break;
            }

            ToggleTurn(); // change current player to the other player



            numOfTurns++;
        }




    }

    private void PrintColoredMessage(ConsoleColor color, string message)
    {
        Console.ForegroundColor = color;
        Console.WriteLine(message);
        Console.ResetColor();
        Console.WriteLine();
    }

    private void ToggleTurn()
    {
        if (WhoseTurn == PlayerOne)
            WhoseTurn = PlayerTwo;
        else
            WhoseTurn = PlayerOne;

    }

    private bool CheckWinner()
    {
        bool currentPlayerWon = Win.DidPlayerWin(WhoseTurn.Piece);

        if(currentPlayerWon)
        {
            PrintColoredMessage(ConsoleColor.Blue, $"Congrats, {WhoseTurn.Name}, you won!");
        }

        return currentPlayerWon;
    }

    public void Save()
    {
        Engine engineToSave = this;
        IFormatter formatter = new BinaryFormatter();
        Console.Write("Enter a save name:");
        string fileName = Console.ReadLine() + ".bin";
        Stream stream = new FileStream(fileName,
                                    FileMode.Create,
                                    FileAccess.Write, FileShare.None);
        formatter.Serialize(stream, engineToSave);
        stream.Close();
    }

    public Engine Load()
    {
        Console.Write("Enter the file name:");
        string fileName = Console.ReadLine() + ".bin";

        IFormatter formatter = new BinaryFormatter();
        Stream stream = new FileStream(fileName,
            FileMode.Open,
            FileAccess.Read,
            FileShare.Read);
        Engine engine = (Engine)formatter.Deserialize(stream);
        stream.Close();

        return engine;
    }
}
```

Now we simply create an instance of a Renderer and a Board to pass the Engine constructor. Engine.Start() will take care of the rest!

```csharp

 class Program
{
    static void Main(string[] args)
    {
        //WinCondition win = new WinCondition();
        Board board = new Board();
        Renderer renderer = new Renderer(board);

        Engine engine = new Engine(board, renderer);

        engine.Start();

    }
}
```

After that, the game is running! Here's a screenshot of it running:  
![Tic Tac Toe Running]({{ site.baseurl }}/images/sample.jpg)

## Refactoring Ideas

After finishing this project I realizes how simple the Player class was. It was so simple that I believe a struct with a Name and Piece item would've work just as well. I don't think there would be any repercussions other than that the Player class would have a reference type and the struct would have value semantics. In this case that is okay, as the state of Player shouldn't change at all once set. Adding onto that, I believe Whitaker's idea in C# Player's Guide of using an enum to set the player piece and board pieces would be best. This would allow only three state instead of a string type. I don't think this would improve our functionality but might make the code more readable and managing states easier.

Currently the Engine class constructor needs a Board and a Renderer. I'm not entirely sure my original reasoning for requiring these parameters but I don't believe it's entirely necessary. I could keep the current constructor and add another one for the occurence of someone making a new Engine without any parameters and create the Board and Renderer inside the constructor.

The UI is also horrendous. While I enjoy the colors, the game seem to take up too many spaces and creates to many new lines. I can improve this by reducing the amount of empty space. Also, I don't believe the way I'm asking for a save or load is all that user friendly. I could instead ask a [Y/N] type of question. Maybe a prompt like: "Do you wish to save or load [s/l]?" This reduces the prompt to one line and isn't too __annoying__.

All in all, this project reinforced the learning from previous chapters of the book and also led to learn about how .NET can serialize data and a little bit about the List<> type and its utility. I'm happy with the finished project and will be writing a run down of a new project shortly.
