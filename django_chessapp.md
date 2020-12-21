# Chess Fan Site


## Introduction

At The Tech Academy, for the last two weeks in every language program we work in teams on a shared project. In the Python program, that app was an Django site for housing personal collections and hobbies. It being Covid-time, we worked remotely. We used Slack, Google Meet, and daily scrum meetings to maintain group cohesion.

A lot of the coding was just setting up models and views in django. If you want to see the whole show, you can [click here](https://github.com/mcleeder/ChessFanSite/tree/main/ChessApp). Following that link will take you to the code I worked on for the site, but not the entire site itself.

Main logic:

-[Chess.com API Request](https://github.com/mcleeder/CodeSamples/blob/main/django_chessapp.md#chesscom-api-request)

-[Beautiful Soup](https://github.com/mcleeder/CodeSamples/blob/main/django_chessapp.md#Beautiful-Soup-web-scraper)



### Chess.com API Request
This is where this app gets started. It asks you for a month, year, and a username. It'll pull all the chess games on file at Chess.com for that user and store them locally. This is one of the first things I wrote in Python that really made me aware of just how quickly and easily you can get things running.

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

This is a helper function that goes with the API request function above. All it does is translate the response data to the local ChessGame() model.

```python
# map a json file from chess.com to a ChessGame object
def json_to_game(json_obj):
    game = ChessGame()
    game.id = json_obj['url'].split('/')[-1]
    game.url = json_obj['url']
    game.time_control = json_obj['time_control']
    game.end_time = datetime.fromtimestamp(json_obj['end_time'])
    game.rated = json_obj['rated']
    game.fen = json_obj['fen']
    game.time_class = json_obj['time_class']
    game.rules = json_obj['rules']
    game.white_player = json_obj['white']['username']
    game.white_player_rating = json_obj['white']['rating']
    game.white_player_result = json_obj['white']['result']
    game.black_player = json_obj['black']['username']
    game.black_player_rating = json_obj['black']['rating']
    game.black_player_result = json_obj['black']['result']
    return game
```

### Beautiful Soup web scraper
Blurb...

```python
def chess_news(request):
    chess_com = requests.get("https://www.chess.com/news")
    soup = BeautifulSoup(chess_com.text, 'html.parser')
    content = []

    for article in soup.find_all('article'):
        headline = article.find('a', class_='post-preview-title').text.strip()
        link = article.find('a', class_='post-preview-title')['href']
        summary = article.find('p', class_="post-preview-excerpt").text.strip()
        date_time = article.find('span', class_='post-preview-meta-content').span['title']
        date_time = date_time.split(",")
        date = f'{date_time[0]}, {date_time[1]}'
        s_article = {
            'headline': headline,
            'link': link,
            'summary': summary,
            'date': date,
        }
        content.append(s_article)

    paginator = Paginator(content, 6)
    page_number = request.GET.get('page')
    page_list = paginator.get_page(page_number)

    context = {'context': page_list}

    return render(request, "ChessApp/chess_news.html", context)
```


### Chess game position render
Blurb...

```python
# map chess final position to list[][]
# for display in template
def render_final_position(fen):
    def try_parse(c):
        try:
            return int(c), True
        except ValueError:
            return c, False

    fens = fen.split(" ")[0].split("/")
    board = [
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""],
        ["", "", "", "", "", "", "", ""]
    ]
    for y in range(0, 8):
        index_c = 0
        for x in fens[y]:
            if not try_parse(x)[1]:
                board[y][index_c] = chess_p[x]
                index_c += 1
            else:
                board[y][index_c] = chess_p[""]
                index_c += try_parse(x)[0]
    return board
```
chess_p = {
    'r': """<i class="fas fa-chess-rook text-dark"></i>""",
    'R': """<i class="fas fa-chess-rook text-light"></i>""",
    'n': """<i class="fas fa-chess-knight text-dark"></i>""",
    'N': """<i class="fas fa-chess-knight text-light"></i>""",
    'b': """<i class="fas fa-chess-bishop text-dark"></i>""",
    'B': """<i class="fas fa-chess-bishop text-light"></i>""",
    'k': """<i class="fas fa-chess-king text-dark"></i>""",
    'K': """<i class="fas fa-chess-king text-light"></i>""",
    'q': """<i class="fas fa-chess-queen text-dark"></i>""",
    'Q': """<i class="fas fa-chess-queen text-light"></i>""",
    'p': """<i class="fas fa-chess-pawn text-dark"></i>""",
    'P': """<i class="fas fa-chess-pawn text-light"></i>""",
    '': """&nbsp;"""
}
```python

```
