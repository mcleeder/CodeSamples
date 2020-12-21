# Chess Fan Site


## Introduction

At The Tech Academy, for the last two weeks in every language program we work in teams on a shared project. In the Python program, that app was an Django site for housing personal collections and hobbies. It being Covid-time, we worked remotely. We used Slack, Google Meet, and daily scrum meetings to maintain group cohesion.

A lot of the coding was just setting up models and views in django. If you want to see the whole show, you can [click here](https://github.com/mcleeder/ChessFanSite/tree/main/ChessApp). Following that link will take you to the code I worked on for the site, but not the entire site itself.

Main logic:

-[Chess.com API Request](https://github.com/mcleeder/CodeSamples/blob/main/django_chessapp.md#Chess.com-API-Request)

-[The checker](https://github.com/mcleeder/CodeSamples/blob/main/Sudoku_Solver.md#the-checker)



### Chess.com API Request



```python
def load_data(request):
    # request method is POST
    if request.method == 'POST':
        form = GetGamesByPlayer(request.POST)
        context = {'form': form}
        if form.is_valid():
            username = form['username'].value().strip()
            year = form['year'].value()
            month = form['month'].value()
            lookup_string = "https://api.chess.com/pub/player/{}/games/{}/{}".format(username, year, month)
            json_request = requests.get(lookup_string)
            # if we find a user on chess.com
            if json_request.status_code == requests.codes.ok:
                json_games = json_request.json()
                for game in json_games['games']:
                    json_to_game(game).save()
                load_status = "Loaded {} games.".format(len(json_games['games']))
                context.update({"load_status": load_status})
            # if no user found on chess.com
            else:
                load_status = "Username not found."
                context.update({"load_status": load_status})
        return render(request, "ChessApp/load_data.html", context)
    # request method is GET
    else:
        form = GetGamesByPlayer
        return render(request, "ChessApp/load_data.html", {'form': form})
```
