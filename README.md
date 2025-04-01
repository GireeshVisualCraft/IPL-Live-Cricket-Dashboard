# Live Cricket Dashboard
Live Cricket Dashboard built in Power BI, featuring real-time match updates, player stats, and interactive analytics.
## Purpose:
The Live Cricket Dashboard is designed to provide real-time updates on cricket matches, as well as historical match data. By leveraging data fetched from Cricbuzz via RapidAPI, the dashboard displays live scores, player statistics, team performances, and match events for both ongoing and completed matches. The data is imported into Power BI through Power Query Editor, using advanced queries to ensure accurate display of all live and post-match records.

---

## RapidAPI:
RapidAPI is a platform that enables developers to connect to a variety of APIs from different providers. It acts as a marketplace that facilitates easy access to and integration with data from numerous APIs, including sports data such as cricket scores, player statistics, and match events. For this dashboard, RapidAPI is used to fetch live and historical cricket data from Cricbuzz, providing up-to-date match information.

[RapidAPI](https://rapidapi.com/hub)

---

## Data Fetching Process:
### 1. Open Power Query Editor in Power BI:
Launch Power BI Desktop and click on **Transform Data** to open the Power Query Editor.

### 2. Create a Blank Query:
In the Power Query Editor, navigate to the **Home** tab, click on **New Source**, and select **Blank Query**.

### 3. Write the Data Fetching Query:
- Enable the **Formula Bar** (if not already visible) by going to the **View** tab.
- Write the following query in the formula bar to fetch data from [Cricbuzz](https://www.cricbuzz.com) via [RapidAPI](https://rapidapi.com/hub):

```
let
    // Define the API URL and headers
    apiUrl = "https://cricbuzz-cricket.p.rapidapi.com/series/v1/9237",
    headers = [
        #"X-RapidAPI-Key" = "**YOUR_API_KEY**",
        #"X-RapidAPI-Host" = "cricbuzz-cricket.p.rapidapi.com"
    ],
    // Create a function to make the API request
    getApiData = (url as text, headers as record) =>
        let
            // Make the request
            response = Web.Contents(url, [Headers=headers]),
            // Parse the JSON response
            jsonResponse = Json.Document(response)
        in
            jsonResponse,
    // Call the function to get the API data
    apiData = getApiData(apiUrl, headers)
in
    apiData 
```

- Replace Your **API KEY** with your actual RapidAPI key, and update the apiUrl with the appropriate series number from [Cricbuzz](https://www.cricbuzz.com/cricket-series/9237/indian-premier-league-2025). Click Done to execute the query.

### 4. Fetch Data from Cricbuzz:
Power BI will fetch the live data from Cricbuzz and display it in the Power Query Editor. You can then expand the matches data to view match details such as scores, teams, and player statistics.

### 5. Extract Required Columns and Organize Tables:
- **Table-1: Match_List**
> Columns: date, match_id, series_id, series_name, match_number, match_format, status, status_description, team1_id, team1_name, team1_short_name, team2_id, team2_name, team2_short_name.
- **Table-2: Live_Match_ID**
>Duplicate of Match_List Table.

### 6. Filter Live Matches:
In Table-2, filter the data to display only live matches or the match statistics you wish to show.

### 7. Fetch Batting, Bowling, Team, and Toss Information:
Create another Blank Query and use the following query to fetch relevant match data:

```
let
    // Define the API URL and headers
    MatchID = Text.From(Live_Match_ID{0}[Match_ID]), // Ensure "Live_Match_ID" is your table name and "Match_ID" is your column name
    LastRul = "scard",
    apiUrl = "https://cricbuzz-cricket.p.rapidapi.com/mcenter/v1/" & MatchID & "/" & LastRul,
    headers = [
        #"X-RapidAPI-Key" = "**YOUR_API_KEY**",
        #"X-RapidAPI-Host" = "cricbuzz-cricket.p.rapidapi.com"
    ],
    // Create a function to make the API request
    getApiData = (url as text, headers as record) =>
        let
            // Make the request
            response = Web.Contents(url, [Headers=headers]),
            // Parse the JSON response
            jsonResponse = Json.Document(response)
        in
            jsonResponse,
    // Call the function to get the API data
    apiData = getApiData(apiUrl, headers)
in
    apiData
```
    
### 8. Organize the Data into Tables:
- **Table-3: Live_Team_Score**
> **Columns:** match_id, innings_id, bat_team_id, bat_team_name, bat_team_short_name, bowl_team_id, bowl_team_name, bowl_team_short_name, ball_number, overs, runrate, runs, wickets, runs_per_ball, bat_team_logo, bat_team_captain_logo, bowl_team_logo, bowl_team_captain_logo, current_score (**custom column:** TEXT.FROM([runs]) & "/" & TEXT.FROM([wickets]) & " (" & TEXT.FROM([overs]) & ")").

- **Table-4: Live_Batsman_Score**
>**Columns:** match_id, inning_id, bat_team_id, bat_team_name, bat_team_short_name, order, bat_id, bat_name, bat_short_name, is_captain, runs, balls, dots, fours, sixes, mins, strikerate, out_description, bowl_id, ones, twos, threes, fives, boundaries, sixes.
- **Table-5: Live_Bowler_Score**
>**Columns:** match_id, inning_id, bowl_team_id, bowl_team_name, bowl_team_short_name, order, bowler_id, bowl_name, bowl_short_name, is_captain, overs, maidens, runs, wickets, economy, no_ball, wides, dots, balls, runs_per_ball.
- **Table-6: Live_Toss_Info**
>**Columns:** toss_winner_id, toss_winner_name, decision.

### 9. Based on Filtered Data:
Use the data from Table-2 to present match-specific details in Table-3, Table-4, Table-5, and Table-6.

### 10. Team Logos and Player List:
Team logos and player details are fetched from a reference Excel sheet.

---
## Pages in the Dashboard:

### 1. Home Page
Series Logo: The Home Page displays the logo of the current cricket series prominently, providing quick identification of the ongoing series for users.
![Page-1](https://github.com/user-attachments/assets/5de3daa7-c2f3-4cb2-a300-750947a9b84a)

---
### 2. Dashboard Page
The Dashboard Page is the central hub of the live match interface, providing a structured layout of various data points and performance metrics.
![Page-2](https://github.com/user-attachments/assets/e33151db-b920-4ec2-ad91-e66cc1c86843)

#### Second Innings Details:
- Team Name: Displays the name of the team currently playing the second innings.
- Live Score: Shows the real-time live score of the second innings.
- Run Rate: Displays the current run rate for the second innings.
- Team Logo: The logo of the team playing the second innings.
- Team Captain Image: Displays the image of the captain of the second innings team.

#### First Innings Details:
- Team Name: Displays the name of the team that played the first innings.
- Live Score: Shows the score of the first innings.
- Run Rate: Displays the run rate of the first innings.
- Team Logo: The logo of the team that played the first innings.
- Team Captain Image: Displays the image of the captain of the first innings team.

#### Current Innings Statistics:
This section is divided into three text boxes that track different batting statistics:
- First Box: Displays the number of singles (1's) and doubles (2's) scored.
- Second Box: Displays the number of fours (4's) hit.
- Third Box: Displays the number of sixes (6's) hit.

#### Current Batting Statistics (Playing - Batsman):
Current Batsmen’s Score: A table displaying the real-time score of the current batsmen at the crease, including:

    Batsman Name
    Runs Scored
    Balls Faced
    Fours Hit
    Sixes Hit
    Strike Rate

#### Bowling Performance Table:
- Team: The name of the bowling team.
- Bowler Name: The name of the current bowler.
- Overs Bowled: The number of overs bowled.
- Runs Conceded: The runs conceded by the bowler.
- Wickets Taken: The number of wickets taken by the bowler.
- Economy Rate: The bowler's economy rate.
- Median Overs: The median overs bowled by the bowler.

#### Batting Performance:
- Team Filter: A dropdown filter to select a team to view detailed batting performance.

Performance Table: Displays batting performance based on the selected team, showing:
    
    Batsman Name
    Runs
    Balls Faced
    Fours Hit
    Sixes Hit
    Strike Rate

#### Contribution Section:
This section displays three doughnut charts representing the contributions of the current batting and bowling teams:

- Sixes Contribution: A doughnut chart showing the contribution of sixes.
- Fours Contribution: A doughnut chart showing the contribution of fours.
- Wickets Contribution: A doughnut chart showing the contribution of wickets taken by the bowling team.
---

### 3. Points Table Page:
The Points Table Page displays the standings of all teams participating in the ongoing cricket series. The page provides an at-a-glance view of each team’s performance, including matches played, won, lost, and net run rate.
![Page-3](https://github.com/user-attachments/assets/a2c3a0b7-0703-4344-8003-e0c5bc825aba)

#### Points Table Overview:
- **Team Name:** The name of the team participating in the series.
- **Team Logo:** The logo of the team.
- **Captain Image:** The image of the team's captain.
- **Matches Played:** The total number of matches played by the team.
- **Won:** The number of matches won.
- **Loss:** The number of matches lost.
- **Net Run Rate (NRR):** The net run rate, used to break ties between teams with the same number of points.

**Filters Section:**

If you click on any team in the points table in this section, the statistics will automatically update to show that team's data. If no team is selected, the statistics for the entire league will be displayed:

    Matches Played
    Matches Won
    Matches Lost
    Net Run Rate
---
## Conclusion:
- The Live Cricket Dashboard offers an interactive and real-time view of cricket matches, combining live scores, player statistics, team performances, and historical data. By leveraging data from Cricbuzz via RapidAPI, the dashboard provides users with up-to-the-minute information, ensuring an engaging experience for cricket fans, analysts, and enthusiasts.

- Through Power BI and the integration of various data points, users can easily navigate between key sections, such as match details, batting and bowling performances, points table standings, and more. The dynamic filters and visualizations, including charts and tables, ensure a clear and insightful presentation of match statistics, contributing to better analysis and understanding of the ongoing series.

- With the ability to access detailed team and player data, live match updates, and a points table overview, this dashboard serves as a comprehensive tool for tracking and analyzing cricket matches in real time. Whether you’re a casual fan or a dedicated analyst, the Live Cricket Dashboard provides the necessary tools to stay informed and engaged throughout the series.
