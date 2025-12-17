# Day 1:
## Task 1: Introduction:
> The unthinkable has happened - McSkidy has been kidnapped. Without her, Wareville’s defenses are faltering, and Christmas itself hangs by a thread. But panic won’t save the season. A long road lies ahead to uncover what truly happened. The TBFC (The Best Festival Company) team already brainstorms what to do next, and their first lead points to the tbfc-web01, a Linux server processing Christmas wishlists. Somewhere within its data may lie the truth: traces of McSkidy’s final actions, or perhaps the clues to King Malhare’s twisted vision for EASTMAS
### Solution:
Just start the virtual machine.

## Task 2: Linux CLI:
### Solution:
```bash
mcskidy@tbfc-web01:~$ ls
Desktop    Downloads  Music     Public      Templates  snap
Documents  Guides     Pictures  README.txt  Videos
mcskidy@tbfc-web01:~$ cat README.txt
For all TBFC members,
Yesterday I spotted yet another Eggsploit on our servers.
Not sure what it means yet, but Wareville is in danger.
To be prepared, I'll write the security guide by tomorrow.
As a precaution, I'll also hide the guide from plain view.
~ McSkidy
mcskidy@tbfc-web01:~$ ls -a
.              .icons                     Downloads
..             .local                     Guides
.ICEauthority  .mozilla                   Music
.Xauthority    .pam_environment           Pictures
.bash_history  .profile                   Public
.bash_logout   .selected_editor           README.txt
.bashrc        .sudo_as_admin_successful  Templates
.cache         .themes                    Videos
.config        .vnc                       snap
.dbus          Desktop
.gvfs          Documents
mcskidy@tbfc-web01:~$ cd Guides
mcskidy@tbfc-web01:~/Guides$ ls
mcskidy@tbfc-web01:~/Guides$ ls -a
.  ..  .guide.txt
mcskidy@tbfc-web01:~/Guides$ cat guide.txt
cat: guide.txt: No such file or directory
mcskidy@tbfc-web01:~/Guides$ cat .guide.txt
I think King Malhare from HopSec Island is preparing for an attack.
Not sure what his goal is, but Eggsploits on our servers are not good.
Be ready to protect Christmas by following this Linux guide:

Check /var/log/ and grep inside, let the logs become your guide.
Look for eggs that want to hide, check their shells for what's inside!

P.S. Great job finding the guide. Your flag is:
-----------------------------------------------
THM{learning-linux-cli}
-----------------------------------------------
```
### **Flag:** THM{learning-linux-cli}
 - Similarly i followed trough and finished the remaining challenges, finding the flags from each one, then completed the objective. I forgot to copy the bash to show it here...


