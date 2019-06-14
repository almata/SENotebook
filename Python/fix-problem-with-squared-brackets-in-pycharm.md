# Fix problem with squared brackets in PyCharm

It seems some users cannot type squared brackets `[]` in PyCharm. This has worked for me (with PyCharm closed, of course):

1. Navigate to **PyCharm.app > Show Package Contents > Contents > bin**.
2. Edit `idea.properties` file.
3. Add this new line at the end `actionSystem.force.alt.gr=true` and save the file.

After reopening PyCharm again, lovely squared brackets `[]` work as expected :)
