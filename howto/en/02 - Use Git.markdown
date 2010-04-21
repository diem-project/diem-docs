## Use Git to get the code

### Clone Diem repo on your computer

The first thing to do is to copy the Diem repo to your computer.

    git clone git://github.com/diem-project/diem.git
    cd diem

Now, you have to choose a branch, like 5.0 or 5.1.

### Checkout a branch

#### 5.0

If you want to use Diem 5.0, create the appropriate branch in your repo.

    git checkout -b 5.0 origin/5.0

This command creates the branch 5.0 in your repo, and makes it track the server 5.0 branch.
It gives you the latest version of the 5.0 branch (5.0.3 when I write these lines).

#### 5.1

If you want to use Diem 5.1, create the appropriate branch in your repo.

    git checkout -b 5.1 origin/5.1

This command creates the branch 5.1 in your repo, and makes it track the server 5.1 branch.
It gives you the latest version of the 5.1 branch (5.1.0-BETA2 when I write these lines).

### Get submodules

External libraries, like symfony, are bundled as submodules. This is roughly equivalent to svn externals.

#### Create submodules

The first time you checkout a branch, you have to initialize its submodules.

    git submodule init

#### Update submodules

To get or update the submodules, run:

    git submodule update

### Manage your branches

To see the branches you have, run

    git branch

It shows the local branches of your repo:

      5.0
    * 5.1
      master

The * means you are currently on the 5.1 branch. To switch to the 5.0 branch, run

    git checkout 5.0

### Upgrade Diem

To upgrade your local Diem to the latest server version of your current branch, run

    git pull

## Use Git to contribute

Thanks to Git and GitHub, it's very easy to contribute to Diem code or documentation.

### Get a GitHub account

It's free: https://github.com/signup/free.
In this doc I'll consider your GitHub username is "marcel".

### Fork diem-project/diem repo

Go on http://github.com/diem-project/diem and click the Fork button.
You now have a copy of the repository in your GitHub account, at http://github.com/marcel/diem.
You can do whatever you want on this repo. Later, you can request to merge your changes in the official diem/project/diem repo.

### Clone your forked repo on your computer

Clone your forked repo on your computer:
[code]
git clone git@github.com:marcel/diem.git
cd diem
git submodule init
git submodule update
[/code]

### Change branch

Firstly, create the branch you want to work on. Let's suppose you're interrested in working on the 5.1 branch.
[code]
git checkout -b 5.1 origin/5.1
[/code]
Here, origin/5.1 represents your forked repo 5.1 branch.
You now have a 5.1 local branch that tracks your remote 5.1 branch.

### Push to your forked repo

Make some commits locally, then push to your GitHub forked repo:
[code]
git push origin 5.1
[/code]

### Get latest updates from diem-project repo

The official Diem repo is updated frequently, don't forget to retrieve changes.

#### Add the diem-project official remote repo

You already have your own remote, named "origin". It's a shortcut to git@github.com:marcel/diem.git.
Add the official repo with this command:
[code]
git remote add official git://github.com/diem-project/diem.git
[/code]
You now have two remotes. Your forked repo, known as "origin", where you can push.
And the official repo, from where you can only pull.
To see the remotes, you can run
[code]
git remote -v
[/code]

#### Pull from the diem-project repo

To get latest updates from diem-project official repo, run:
[code]
git pull official 5.1
[/code]
[code]
git remote -v
[/code]

### Contribute to the diem-project repo

If you feel happy with your changes and want to include them in Diem, push your changes to your GitHub repo with
[code]
git push origin 5.1
[/code]

Then on your GitHub page (http://github.com/marcel/diem) click the "Pull request" button.
We will examine your changes quickly.
This is the very best way to contribute to Diem development, and makes us very, very happy.

### Contribute to Diem documentation

Diem documentations have a GitHub repository, too: [http://github.com/diem-project/diem-docs](http://github.com/diem-project/diem-docs).
You can fork it to contribute, the same way. It makes us happy too.