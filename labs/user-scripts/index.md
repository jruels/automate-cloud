# Bash user scripts
Shell scripts are an important tool in a system administrator’s toolbox. Whether you use them for automation, reporting, or just ad-hoc to process large volumes of repetitive work, it’s important to understand how to make scripts work for you. In this hands-on lab, we will take a look at shell scripts, including basic concepts.

In this lab, you're a system administrator tasked with adding a large number of users — not all at once, but over time — just enough to be irritating to do by hand, one at a time.

First, you'll create a script that you can use to add batches of users by adding them to a support file and then running the script. You'll then check the exit code to see what the result was, and then confirm the addition of the users in the various user-related files.

Next, you'll get tired of having to set the user's passwords by hand — one by one — so you'll update the script to set a default password for the users being created.

Finally, you'll realize that you'll have some reasonable turnover, so you'll apply the same logic as in the `create_user` script to make a `delete_user` script and delete your outgoing people in batches.

Objectives: 

* The following user accounts must exist on the system
  * `mary`,` jim`, `diego`, `jason`, `sherry`
* Each account must have a password of `bash`
* Each account must be a member of the `sudo` group
* Add a user by hand — and realize how much work it is!
* Create a script `create_users.sh` to add users in a batch from an input file.
* Test if the script works and if the desired results occur.

* Create a user deletion script `delete_users.sh`

- Delete the recently added users.



## Bonus

* Extend the scripts to be idempotent. They should check if the user exists and exit with a message stating this.




## Congrats!

