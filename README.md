# bisync - a bidrectional sync script

**bisync** is a shell script that provides bidirectional synchronization of two directories. It uses `rsync` to copy files in both directions while tracking file deletions between sync runs. Files are only removed on one side if they have not been modified on the other side since the last sync. This ensures that deletions are applied safely.

**bisync** was delevoped and runs on macOS. It should easily be portable to Linux. The stat command needs to be modified for that.

## Features

- **Bidirectional Sync:**  
  Synchronize two directories so that, after the sync, both directories are identical regardless of which one is considered the source or target.

- **Deletion Tracking:**  
  Tracks files that were deleted after the previous sync by comparing file timestamps, ensuring that only files which have not been modified on the opposite side are removed.

- **Dry Run Mode:**  
  Simulate the synchronization process without making any actual changes.

- **Robust Temporary File Handling:**  
  Utilizes `mktemp` to create unique temporary files and a `trap` mechanism to ensure that these files are cleaned up if the script exits or is interrupted.

## Usage

Run the script from the command line with the following syntax:

```bash
bisync -s source_path -t target_path [options]
```

### Options
-s, --source-path PATH
Specifies the path to the source directory (no trailing slash).

-t, --target-path PATH
Specifies the path to the destination directory (no trailing slash).

-n, --dry-run
Executes a simulation of the sync process without making any changes.

-h, --help
Displays the help message and usuage.


## How It Works

### Deletion Detection:
bisync uses rsync -av -n --delete in both directions to generate lists of files that would be deleted. These lists are stored in temporary files.

### Safe Deletion:
For each file marked for deletion, the script compares its current modification timestamp with the timestamp of the last sync run. If the file on the other side was modified after the previous sync, the deletion is skipped.

### Synchronization:
After processing deletions, the script performs standard rsync operations (without --delete) in both directions to copy new and updated files.

### State Tracking:
A unique hash (based on the paths) and a timestamp from the previous run are stored in the user's Application Support directory. These files help determine if a sync is a "first run" or a subsequent run.


### Temporary Files and Cleanup

The script creates temporary files using mktemp for storing deletion lists. A trap is set up at the beginning of the script to automatically remove these files on exit or if the script is interrupted, ensuring no leftover temporary files remain.

## Installation

### Clone the Repository
```
git clone https://github.com/lisanet/bisync.git
```

### Run the install script
You'll be asked to enter your admin password. The install script copies the bisync script into /usr/local/bin

```
cd bisync
./install.sh
```

### Uninstalling bisync
To uninstall bisync, simply run the uninstall script. You'll be asked to enter your admin password.

```
./uninstall.sh
```
The uninstall sscript remove the following file 
`/usr/local/bin/bisync`
and the support directory
`$HOLME/Library/Application Support/de.lisanet.bisync`


## License

This project is licensed under the BSD 2-clause license. See the LICENSE file for details.

## Contributing

Contributions, bug reports, and feature requests are welcome. Please open an issue or submit a pull request if you have any improvements or suggestions.

## Disclaimer

bisync is provided "as is" without any warranty. Use at your own risk. Always ensure that you have current backups of your data before performing synchronization operations.
