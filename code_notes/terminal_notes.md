#Terminal notes

+ **‘cmd-shift-p’** : command palette- lists every command

+ **pwd**: print working directory
	~ : /home/codebox
+ **ls**: lists all the stuff you’ve made.
	+ new stuff is in purple
+ **cd**: change directory
you can only cd into folders, but not files.
+ **cd ..** : level up to the parent folder
go back to home folder
+ **cd ~**: brings you to the origin 
+ **touch** : create file
+ **mkdir**: make directory
+ **rm** : **** PERMANENT DELETE****
+ **mv**: move (and rename: mv “giveoldname” “new_name”)
 + go to the destination of the file you want to move (ex. desktop). Then, type the file name + the file path. The "." stands for the current directory (desktop in this case) **(terminal_ notes ./Workspace/code_notes)**
+ **cp** : copy files/directories/directories+files
	+ **cp (file-name) (destination directory)** = copy single file to destination directory
	+ **cp (file-1) (file-2) (/home/usr/rapid/)** = copy 2 files to destination absolute path directory /home/usr/rapid/
	+ **cp *.c(current directory) (destination directory)** = copy all current directory to destination directory
	+ **cp -R (directory+its files) (dest-directory)** = copy all files and directories in folder recursively to destination
	+ **cp -u * (dest-directory)** = update all files in current directory - copy only newer files to destination directory
+ **pwd**: print working directory 
+ **hostname**: my computer's network name
+ **mkdir**: make directory
+ **rmdir**: remove directory
+ **pushd**: push directory
+ **popd**: pop directory
+ **less**: page through a file
+ **cat**: print the whole file (concatenates the whole file(s) to the screen with no paging or stopping)
+ **xargs**: execute arguments
+ **find** :find files
+ **grep**: find things inside files (grep new *.txt— grep “search_phrase” “wherever”)
+ **man**: read a manual page
+ **apropos**: find what man page is appropriate
+ **env**: look at your environment
+ **echo**: print some arguments
+ **export**: export/set a new environment variable
+ **exit**: exit the shell
+ **sudo**: DANGER! become super user root DANGER!
+ **chmod**: change permission modifiers
+ **chown**: change ownership


** to change colors or font thickness type in cd ~ :  subl ~/.bash_profile






+ **“||”**: or
	+ “if the name is Chris or Katy” = 
“if name == “Chris” || name == “Katy””
	+ DOES NOT WORK: “if name == (“Chris” || “Katy”)” MUST BE EXPLICIT
+ **“&&”**: and
+ **“!”**: not

