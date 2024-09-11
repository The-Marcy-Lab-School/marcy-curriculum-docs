# Configure Your Github Account

You will be using Github individually and collaboratively. We will set up your development environment to be able to communicate with your Github account. You'll be able to clone code from a remote repo using SSH and make changes to it on your computer.

**Table of Contents:**
- [Create a Github Account](#create-a-github-account)
- [Configuring Github with your Terminal](#configuring-github-with-your-terminal)
- [Create a Repository on Github](#create-a-repository-on-github)
- [Name your repository](#name-your-repository)
- [Add SSH Key](#add-ssh-key)
- [Clone Using SSH](#clone-using-ssh)


## Create a Github Account

[Create a free Github Account](https://github.com/join). Remember the email and username that you enter. If you already have one, you can skip to the next step.

## Configuring Github with your Terminal

1. In your Terminal application (the folder you're in doesn't matter), we first want to ensure that you can use the `git` tool for the next few steps. 
   1. Start by running the command `git -v` which will print the current version of the `git` tool on your computer. 
   2. If you are using a Mac and this is your first time, a popup called **Install Command Line Developer Tools** will appear. Follow the instructions to install on your computer.

2. Once this is done, in your Terminal, run the following lines. Be sure to replace `Your Name` and `Your Github Email Address` with your actual Github login information:

```sh
git config --global user.name "Your Name"
git config --global user.email "Your Github Email Address"
git config --global credential.helper store
```

2. Confirm that the configuration was successful by running `git config --global user.name`. The terminal should print out your name. Next, run `git config --global user.email`. The terminal should print our your email address.

## Create a Repository on Github

1. Navigate to Github in the browser and log in. ON the left, click the button to create a new repository.

![1](./img/github-setup-1.png)

## Name your repository
Name your repository `my-first-repo`. Choose to add a README.md file. Create the repository.

![2](./img/github-setup-2.png)

## Add SSH Key

While we're configuring GitHub, we should add a new SSH key to allow you to push and pull from Github using SSH. 

1. First, check if you already have an SSH key by running `ls ~/.ssh`. If the terminal prints out a file(s) that look like `id_ed25519.pub` then you already have a key.

2. If the running previous step printed "No such file or directory", then run `ssh-keygen -t ed25519` to create a key.
    - It will ask you to enter a file to save the key, leave it blank and press enter. It will say that it created the directory `/Users/<your_username>/.ssh`
    - It will ask you to enter a passphrase, leave it blank and press enter twice to confirm.
    - If you've done every correctly, you should be something like this printed to your terminal:

![4](./img/ed25519.png)

Run `cat ~/.ssh/ed25519.pub` in your terminal. Copy the output (starting from `ssh-ed25519` and ending in `.local`). You'll need it for the next step

1. Navigate to the homepage of Github in your browser. Go to your account settings:

![5](./img/github-setup-5.png)

Click "SSH and GPG Keys":

![6](./img/github-setup-6.png)

Click the "New SSH key" button:

![7](./img/github-setup-7.png)

Put a description title about what environment the SSH key is associated to. For example, if you are setting up a Marcy Macbook, name it something like "Marcy Macbook", or if it's your personal laptop you can name it "Personal Marcy Computer". Open the 'Key type' dropdown and select 'Authentication Key'. Paste the key in the text area and click "Add SSH key".

![9](./img/addSSHkey.png)

## Clone Using SSH

1. Go back to your repository on Github. Then, click on the Code button and make sure the pop-up has "SSH" underlined and selected. The url should start with `git@github.com:...`. If this is not the case, click the link that say `SSH`. Copy that URL in the text box.

![13](./img/github-setup-13.png)

2. Back in your code editor's terminal, make sure that you have a `Unit-0` folder and that your "working directory" is your `Unit-0` folder. If not, `cd Unit-0` to jump into it. Clone down the project using `git clone repo_ssh_url`, replacing `repo_ssh_url` with the URL you copied from the previous step. If asked, "Are you sure you want to continue connecting", type `yes`. 

3. Then change directory into your project `cd my-first-repo` or whatever you named your repository. 

4. Once your repo can been cloned down, use the vsCode IDE to update the `README.md` **inside of your `my-first-repo` project**. This is not to be confused with the `README.md` in the root folder of your environment. You should specifically open and edit the one in your `my-first-repo` folder. **Add a 3-4 sentence bio about yourself**. Be sure to save the file.

5. Push the changes back up to Github using best practices. In the terminal, run:
```sh
git status
git add README.md
git commit -m "added bio"
git push
```
6. Go back to Github to view your repository in the browser. Refresh the page and confirm that you see your newly added bio!

# Final Step

Once you have completed all the steps in these instructions, reach out to a technical instructor to verify you are finished. 