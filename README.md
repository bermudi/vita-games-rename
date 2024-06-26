# Overview of the Program

The Python script is designed organize directories containing PlayStation game data by examining param.sfo files, which contain metadata about the games. It performs actions such as printing the contents of these files and organizing game directories.

# param.sfo File Structure

param.sfo files are key to PlayStation game directories. They hold metadata about the game in a simple key-value format. Here's what the structure typically includes:

TITLE: The official title of the game.
STITLE: A shorter or simplified title.
PUBTOOLINFO: Publisher information (presence indicates a game, absence typically indicates homebrew).
Other information like version, category, and region.

# Game Directory Structure

Game directories typically have the following structure:

```
Game_Root/
│
├── eboot.bin      # The executable file for the game
├── sce_sys/
│   └── param.sfo  # Metadata file
└── sce_pfs/
    └── ...        # Other game data files

```

The script checks for the presence of eboot.bin and sce_sys/param.sfo to validate if a directory is indeed a game directory.

# Command-Line Options

The script supports several command-line options to specify its behavior:

* No parameters: Assumes the current directory is the root of a game, and looks directly for sce_sys/param.sfo within it to print its contents.
* `--organize`: Organizes all found game directories under the library root into new directories named after their STITLE values from param.sfo.
* `--txt-paramsfo`: Generates a text file in the root of each game directory that lists all the metadata found in the param.sfo file.
* path: Optional; Treats the given path as the library root containing multiple game directories and performs operations on each valid game directory found. If not provided, the current directory is assumed to be the root of a game directory.

# Program Flow and Functions

Parsing Command-Line Arguments: The script starts by parsing command-line options using argparse.
Reading param.sfo: A function read_sfo_data opens the param.sfo file, reads the contents, and parses them into a dictionary.
Checking Directory Validity: The script uses a function to check if a directory contains a valid game by looking for eboot.bin and sce_sys/param.sfo.
Organizing Games: Based on the STITLE from param.sfo, game directories can be reorganized into new directories named after the STITLE.
Generating Text Files: For each game directory, a text file summarizing the param.sfo contents is created at the directory level.
Handling Errors and Outputs: The script includes error handling to manage missing files, permission issues, and other potential runtime errors. It also prints outputs based on the operations performed, such as the success of directory reorganizations and file generations.


```
                        Start
                          │
                          ▼
             Parse Command-Line Arguments
                          │
                          ▼
       Is a path specified on the command line?
                 /                 \
              Yes                   No
               │                     │
               ▼                     ▼
    Treat path as                Assume current directory
    library root                 is game root directory
       (root of                               │
    multiple games)                           ▼
        │                        Look for `sce_sys/param.sfo`
        │                        in the current directory
        │                                     │
        ▼                                     ▼
    Iterate over directories     Print contents of `param.sfo`
    in the path                               │
        │                                     ▼
        ▼                        Is `--txt-paramsfo` specified?
    For each directory:                       │
        │                                    Yes
        ▼                                     ▼
    Check if directory           Generate a text file in the current
    is a valid game root         directory listing `param.sfo` contents
        │                                     │
        ▼                                     ▼
    Is `--organize` specified?   (End of processing)
        │
        Yes
        ▼
    Organize games into directories
    named after `STITLE` from `param.sfo`
        │
        ▼
    Is `--txt-paramsfo` specified?
        │
        Yes
        ▼
    Generate a text file in the root
    directory listing `param.sfo` contents
        │
        ▼
    (End of processing)
```


# Explanation
Start and Argument Parsing: The program begins by interpreting the command-line arguments to determine the path and which flags are active.
Path Decision:
No Path Specified: Assumes the script is executed in a game root directory, immediately searching for sce_sys/param.sfo to display its contents.
Path Specified: Considers the provided path as a library root, which potentially contains multiple game directories.
Game Directory Validation: Validates each directory in the specified path to confirm it's a game directory by checking for eboot.bin and param.sfo.
Operations Based on Flags:
Organizing: If --organize is flagged, directories are restructured based on STITLE.
Text File Generation: The --txt-paramsfo flag triggers the creation of text files summarizing param.sfo contents, applicable in both scenarios of path specified or not.

Expected unorganized structure for library directory:

```
library_root/
│
├── PCSA00029/                    # Game A
│   ├── sce_sys/
│   │   └── param.sfo
│   ├── sce_pfs/
│   └── eboot.bin
├── PCSA00740/                    # Homebrew A
│   ├── sce_sys/
│   │   └── param.sfo
│   └── eboot.bin
└── PCSE00914/                    # Game B
      ├── sce_sys/
      │   └── param.sfo
      ├── sce_pfs/
      └── eboot.bin
```

Expected result after orginizing library:

```
library_root/
│
├── Games
│   ├── Game_A
│   │   └── PCSA00029/                  # Game A
│   │       ├── sce_sys/
│   │       │   └── param.sfo
│   │       ├── sce_pfs/
│   │       ├── eboot.bin
│   │       └──Game_A.txt (only when run with --txt-paramsfo)
│   └── Game_B
│       └── PCSE00914/                  # Game B
│           ├── sce_sys/
│           │   └── param.sfo
│           ├── sce_pfs
│           └── eboot.bin
└── Homebrew
    └── Homebrew_A
        └── PCSA00740/                  # Homebrew A
            ├── sce_sys/
            │   └── param.sfo
            └── eboot.bin
```
