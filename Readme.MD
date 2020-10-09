
Simple line
Third line
Second line

Stack Overflow
About
Products
For Teams
Search…
 
By using our site, you acknowledge that you have read and understand our Cookie Policy, Privacy Policy, and our Terms of Service.

Home
PUBLIC
Stack Overflow
Tags
Users
FIND A JOB
Jobs
Companies
TEAMS
What’s this?
Free 30 Day Trial
How does git store files?
Asked 8 years, 10 months ago
Active 1 year, 7 months ago
Viewed 85k times

Report this ad

244


118
I just started learning git and to do so I started reading the Git Community Book, and in this book they say that SVN and CVS store the difference between files and that git stores a snapshot of all the files.

But I didn't really get what they mean by snapshot. Does git really make a copy of all the files in each commit because that's what I understood from their explanation.

PS: If any one has any better source to learn git I would appreciate it.

git
share  improve this question  follow 
edited May 2 '14 at 6:47
user456814
asked Nov 19 '11 at 23:17

mteffaha
2,72544 gold badges1313 silver badges2020 bronze badges
21
Here's a brilliant post that explains in detail how git works. What you're looking for is probably the § about the object database. – greg0ire Nov 20 '11 at 0:04
Excellent article that contains links to other great resources. I've had fun with these for a couple of hours. – mihai Mar 11 '15 at 14:07
2
I found this really nice article describing git from inside out: maryrosecook.com/blog/post/git-from-the-inside-out – Sumudu Jan 15 '16 at 2:38
add a comment
2 Answers

282

Git does include for each commit a full copy of all the files, except that, for the content already present in the Git repo, the snapshot will simply point to said content rather than duplicate it.
That also means that several files with the same content are stored only once.

So a snapshot is basically a commit, referring to the content of a directory structure.

Some good references are:

git.github.io/git-reference
You tell Git you want to save a snapshot of your project with the git commit command and it basically records a manifest of what all of the files in your project look like at that point

git immersion
Lab 12 illustrates how to get previous snapshots

"You could have invented git (and maybe you already have!)"

What is a git “Snapshot”?

Learn GitHub

The progit book has the more comprehensive description of a snapshot:

The major difference between Git and any other VCS (Subversion and friends included) is the way Git thinks about its data.
Conceptually, most other systems store information as a list of file-based changes. These systems (CVS, Subversion, Perforce, Bazaar, and so on) think of the information they keep as a set of files and the changes made to each file over time

delta-based VCS

Git doesn’t think of or store its data this way. Instead, Git thinks of its data more like a set of snapshots of a mini filesystem.
Every time you commit, or save the state of your project in Git, it basically takes a picture of what all your files look like at that moment and stores a reference to that snapshot.
To be efficient, if files have not changed, Git doesn’t store the file again—just a link to the previous identical file it has already stored.
Git thinks about its data more like as below:

snapshot-based VCS

This is an important distinction between Git and nearly all other VCSs. It makes Git reconsider almost every aspect of version control that most other systems copied from the previous generation. This makes Git more like a mini filesystem with some incredibly powerful tools built on top of it, rather than simply a VCS.

Jan Hudec adds this important comment:

While that's true and important on the conceptual level, it is NOT true at the storage level.
Git does use deltas for storage.
Not only that, but it's more efficient in it than any other system. Because it does not keep per-file history, when it wants to do delta compression, it takes each blob, selects some blobs that are likely to be similar (using heuristics that includes the closest approximation of previous version and some others), tries to generate the deltas and picks the smallest one. This way it can (often, depends on the heuristics) take advantage of other similar files or older versions that are more similar than the previous. The "pack window" parameter allows trading performance for delta compression quality. The default (10) generally gives decent results, but when space is limited or to speed up network transfers, git gc --aggressive uses value 250, which makes it run very slow, but provide extra compression for history data.

share  improve this answer  follow 
edited Feb 19 '19 at 17:47

Przemek
17611 silver badge88 bronze badges
answered Nov 19 '11 at 23:47

VonC
988k408408 gold badges34063406 silver badges40024002 bronze badges
4
@JanHudec good point. I have included your comment in the answer for more visibility. – VonC Jan 3 '13 at 14:00
1
Does anyone know the computer science term for the Git-like storage pattern, aka hash-based value store? (or something similar) – Joannes Vermorel Nov 24 '14 at 12:00
36
In the context of the OP's actual question, the first paragraph seems really misleading. It's not until you get to the final paragraph that we learn that, oh yes, fact Git does "store [...] differences between files. Really wish that info was flagged up top and not buried so deep. That said, thanks at least including the real story somewhere in your answer ;) – Josh O'Brien Jan 27 '15 at 20:50 
1
@NickVolynkin Great! I am glad those answers are finding a larger audience. – VonC Aug 21 '15 at 14:15
1
Another good book: Git From The Bottom Up: ftp.newartisans.com/pub/git.from.bottom.up.pdf – Jonas Berlin Aug 21 '17 at 19:34
show 14 more comments

Report this ad

49

Git logically stores each file under its SHA1. What this means is if you have two files with exactly the same content in a repository (or if you rename a file), only one copy is stored.

But this also means that when you modify a small part of a file and commit, another copy of the file is stored. The way git solves this is using pack files. Once in a while, all the “loose” files (actually, not just files, but objects containing commit and directory information too) from a repo are gathered and compressed into a pack file. The pack file is compressed using zlib. And similar files are also delta-compressed.

The same format is also used when pulling or pushing (at least with some protocols), so those files don't have to be recompressed again.

The result of this is that a git repository, containing the whole uncompressed working copy, uncompressed recent files and compressed older files is usually relatively small, two times smaller than the size of the working copy. And this means it's smaller than SVN repo with the same files, even though SVN doesn't store the history locally.

share  improve this answer  follow 
edited Jan 10 '19 at 16:33

Gautham Pughazhendhi
32211 gold badge22 silver badges1616 bronze badges
answered Nov 19 '11 at 23:49

svick
205k4747 gold badges337337 silver badges458458 bronze badges
1
ah so mercurial is more space effecient – Ben Sep 13 '16 at 23:47
add a comment
Your Answer
Sign up or log in
Post as a guest
Name
Email
Required, but never shown

By clicking “Post Your Answer”, you agree to our terms of service, privacy policy and cookie policy

Not the answer you're looking for? Browse other questions tagged git or ask your own question.
The Overflow Blog
Static site generation with single page app functionality? That’s what’s…
Podcast 276: Ben answers his first question on Stack Overflow
Featured on Meta
Goodbye, Prettify. Hello highlight.js! Swapping out our Syntax Highlighter
Responding to the Lavender Letter and commitments moving forward
How does the highlight.js change affect Stack Overflow specifically?

Report this ad
AVM GmbH
AVM GmbH
Communications
We have great benefits!
Sportgemeinschaften sowie vielfältige Weiterbildungsangebote
Teamevents (Sommerfest, Weihnachtsfeier) und soziales Engagement
Flexible Arbeitsbedingungen
Prämienzahlung und vermögenswirksame Leistungen
+ 6 more benefits
Learn more
Linked
3
Where are the project files stored in a git repository `.git` folder?
3
Do duplicate files increase git repo size?
-2
Where sources committed to Git are stored in the server?
0
How does Git achieve storage efficiency when storing multiple versions of a file?
1
Does a blob contain the current status a file or only the last change?
-3
How git manage to store the files branch wise?
0
How can git compact files so much?
393
Why is Git better than Subversion?
124
What does it mean to squash commits in git?
70
What is a git “Snapshot”?
See more linked questions
Related
7018
How to remove local (untracked) files from the current Git working tree
12157
What is the difference between 'git pull' and 'git fetch'?
9260
How do I undo 'git add' before commit?
21634
How do I undo the most recent local commits in Git?
2882
Find and restore a deleted file in a Git repository
7510
How do I force “git pull” to overwrite local files?
5703
How to make Git “forget” about a file that was tracked but is now in .gitignore?
17313
How do I delete a Git branch locally and remotely?
7628
How do I revert a Git repository to a previous commit?
8882
How do I rename a local Git branch?
Hot Network Questions
Short Sci-Fi story from the 60's, was there any follow up story?
Am I starting my story too late?
What is the meaning of "dead draw"?
How would I coordinate meetings with people on Mars?
If objects in motion experience time differently, how does my body stay synced when I move my legs or arms?
Book where aliens live in an ocean contained in a special camp
Extra Meaningful way of saying "I love my children, who are no longer with me"
Was Donald Trump treated with pharmaceuticals derived from fetal stem cells?
Broadcast signal deviating with time
Can a reduction of employer taxation lead to more equal income?
Are environmentalists responsible for Californian forest fires?
Does tar roof patch on EDPM rubber roof cause damage?
How to get my parents to take my Mother's cancer diagnosis seriously?
How far could civilisation develop within one lifetime - starting from nothing?
Strange minimalist power supply, which IC?
What's the term for finding an attestation of a word that predates the earliest known one?
Why do Senators and Representatives miss so many votes?
Why are DFT simulations/calculations called First Principles Structural calculations? What is 'First Principles'?
What do professors do if they receive a complaint about incompetence of a teaching assistant?
Why should I be Bayesian when my dataset is large?
Should I apply to schools I am not sure I would attend?
Why do people look directly at the keyboard when playing piano in videos?
How do I plumb this kitchen sink to stop the leak and improve drainage
Very particular kind of 4-manifolds. Classification
 Question feed

STACK OVERFLOW
Questions
Jobs
Developer Jobs Directory
Salary Calculator
Help
Mobile
Disable Responsiveness
PRODUCTS
Teams
Talent
Advertising
Enterprise
COMPANY
About
Press
Work Here
Legal
Privacy Policy
Contact Us
STACK EXCHANGE
NETWORK
Technology
Life / Arts
Culture / Recreation
Science
Other
Blog
Facebook
Twitter
LinkedIn
Instagram
site design / logo © 2020 Stack Exchange Inc; user contributions licensed under cc by-sa. rev 2020.10.7.37758

 
