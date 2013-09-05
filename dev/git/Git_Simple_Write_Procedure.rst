Contributing to the SALOME project with Git
===========================================

This short document indicates the process to be followed by anyone wishing to contribute code to the SALOME project using Git. If you read this document, you should have received the credentials allowing you to write your changes into the central repository.

Get your copy of the central repository
---------------------------------------

1. Clone the latest version of the repository for the module you want to modify::    

     git clone ssh://<proper-final-url>

   or::

     git clone https://<proper-final-url>
  
  This retrieves a local copy of the module's repository onto your machine. 
   
  .. note:: The later should be used to work behind a proxy (see :ref:`special-instructions-for-https` )
  

2. If you were already given a branch name to work with, you can simply retrieve it and start working::

    git checkout -b <branch_name> origin/<branch_name>


  This creates a local copy of the branch, set up to be a copy of the remote branch on the central server. You can jump directly to the next section (Workflow) telling how to commit and publish changes.

3. Otherwise you need to create and publish a new branch in which you will do your changes (you are not allowed to commit changes directly into the main branch 'master'). First create the local version of the new branch::

    git checkout master
    git checkout -b <xyz/short_desc>
  

  where <xyz> are you initials (John Smith gives 'jsm') and <short_desc> is a small string indicating what changes will be made. For example::
    
    jsm/new_viewer

  
  The last command creates the branch locally, and update your working directory (i.e. the source code) to match this branch: every change and commit you make from now on will be stored in this branch.

4. Publish your branch to the central repository for the first time::

    git push -u origin <xyz/short_desc>
  
  
  The option "-u" ensure your local branch will be easily synchronized with the remote copy on the central server. With older versions of Git you might have to execute the above in two steps::

    git push origin <xyz/short_desc>
    git branch -u origin/<xyz/short_desc>
  
  
Workflow
--------

1. If you didn't update your local copy for a while, update it with the following command. This retrieves from the central server all changes published by other people working on your branch. This step is not necessary if you just initialized your repository as described above::

    git pull
  
2. Do your changes, compile. 
3. Perform the appropriate tests to ensure the changes work as expected.
4. If you create a new source file, you need to make Git aware of it::

    git add <path/to/new/file>
  
5. When everything is ready you can commit all your changes into your local repository::

    git commit -a
  
  The "-a" option tells Git to automatically take all the changes you made for the current commit. You will be asked to enter a commit message. Please, *please*, write something sensible. A good format is::

    GLViewer: enhance ergonomy of the 3D view
  
    User can now zoom in and out using the mouse wheel. Corresponding keyboard shortcuts have also been added.

  i.e. a first short line containing the class/code entity that was modified, and a short description of the change. Then a blank line, and a long description of the change. Remember that this message is mainly for other people to understand quickly what you did.
  
  
  At this point, the changes are just saved in your local repository. You can still revert them, amend the commits, and perform any other operation that re-writes the local history.
  
6. Once you feel everything is ready to be seen by the rest of the world, you can publish your work. The first step is to synchronize again with any potential change published on the central repository while you were working locally::

    git pull
  
7. At this stage, two situations can arise. If nothing (or some unrelated stuff to your work) happened on the central repository, Git will not complain (or potentially do an automatic merge). You can inspect the latest changes committed by others with commands like::

    git log
    gitk 

  If you notice changes made by others that can affect what you're working on, it might be a good idea to recompile and retest what you have done, even if Git did the merge automatically. Once you are happy, you can directly go to step 9.

8. Conflict resolution. If a message saying "CONFLICT: automatic merge FAILED" appears, it means that some changes made by others are in conflict with what you committed locally (typically happens when the other person modified a file that you also changed). In that case, you need to integrate both changes: edit the file so that both changes work together (or so that only one version is retained). Conflicts are marked in the file like this::

    <<<<<<< HEAD:mergetest
    This is my third line
    =======
    This is a fourth line I am adding
    >>>>>>> 4e2b407f501b68f8588aa645acafffa0224b9b78:mergetest

  Once you resolved the conflict, re-compiled and re-tested the code, you need to tell Git that the file is no more in conflict::
  
    git add <the_file>

  You can then finish the merge operation by committing the whole thing::
  
    git commit -a
  
  In this peculiar case (conflict resolution) you will see that Git offers you a default message (merge message). You can complete this message to indicate for example how the conflict was solved. 
  
9. When all conflicts are solved (and the code has been compiled and tested again if needed) you can finally publish your work to the central repository::

    git push

  This makes your changes visible to others.

10. Once all your changes have been committed (potentially several commits) and you feel your modification is ready to be integrated in the main development line (i.e. to be considered for the next release), you can notify an administrator of the project to ask for your changes to be merged in the *master* branch. 

.. _special-instructions-for-https:

Special instructions for https protocol
--------------------------------------

Certificates
~~~~~~~~~~~~
To be able to use the https protocol you will first have to install the appropriate certificate (let's call it *ca.crt*)
On Debian the procedure is described here::

  /usr/share/doc/ca-certificates/README.Debian

As explained in this file you will have to copy the certificate *ca.crt* in::

  /usr/local/share/ca-certificates/

and run::

  update-ca-certificates

Configure Git for http(s) protocol behind a proxy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
If you want to use Git via http (or https) protocol behind a proxy configure Git by executing the following command::

 git config --global http.proxy http://<login_internet>:<password_internet>@aproxy:aport

