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