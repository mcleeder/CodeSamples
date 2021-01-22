# Code Samples


## C# Samples:
[Internship Project: Theatre Company](https://github.com/mcleeder/TheatreCompany/): This was an ASP.NET MVC webapp made for a local theater group in Portland, OR. I worked with a group of developers for two weeks doing some back and front end stories. Being 2020, we worked remotely and used video chats for our scrums.

[Sudoku solving web app](https://github.com/mcleeder/SudokuSolverWebApp/blob/master/README.md): MVC web app that solves sudoku puzzles. This was a side project of mine just for extra study while I was in boot camp.


## Python Samples:

[Internship Project: Chess Fan Site](https://github.com/mcleeder/ChessFanSite/): This was a little sub-app I wrote with a team of developers working on a Django web app during an internship at Prosper IT Consulting. I worked with a group of developers for two weeks, being 2020, we worked remotely and used video chats for our scrums.

[File Lookup & Rename Script](ISBN_file_rename.ipynb): My partner is a freelance editor. One of her clients asked her to pick up a task to rename over 500 product cover image files. This likely would have taken an entire day for a human and would have been super boring. Python does it almost instantly. It took me a few hours to make sure the source data checked out and to learn enough Pandas to write the script.

The task was that the files were named by their product ID, but they wanted them by ISBN number instead. A spreadsheet was provided for matching those two fields. For example, ACBN101-1.jpg and ACBN101-2.jpg would need to become 1234567890123.jpg and 1234567890123_1.jpg. Not every file had a pair, and not every file was included in the reference Excel spreadsheet. So the script needed a bit of extra logic to look for possible pairs, and needed a way to fail gracefully if it couldn't find an ISBN.

I used Jupyter Notebooks to write the script and Pandas to import and query the Excel file. And the rest was just some regular ol' Python.

