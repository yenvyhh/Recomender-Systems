# Projekt 4 - Recommender Systems
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/yenvyhh/Recomender-Systems/main?filepath=Recommender%20Systems%20-%20Projekt%204.ipynb)


**Zu Beginn erfolgt die Defintion von Spaltennamen:**
column_names = ["user_id","item_id","rating","timestamp"]
**Die Daten werden nun importiert,als DataFrame mit den vorab definierten Spaltennamen abgespeichert und das Head wird angezeigt:**
df= pd.read_csv("U.data", sep="\t",names=column_names)
df.head()
**Nach Ausführung sollten von der importierten Datei die ersten 5 Zeilen mit den Spalten user_id, item_id, rating und der timestamp angezeigt werden** 

**Um die item_ids mit den Filmtiteln zu verknüpfen müssen der zweite Datensatz importiert werden und als DataFrame abgespeichert werden. Daraudhin erfolgt die Zusammenführung der DataFrames:**
movie_titles = pd.read_csv("Movie_Id_Titles")
df= pd.merge(df,movie_titles,on="item_id")
df.head()
**Nun sollte eine Liste wie zuvor nur mit der zustälichen Spalte "title" zusehen sein (im Beispiel sollte jetzt bei title 5x Starwars(1977) stehen).

**Darauffolgend erfolgt eine EXPLORATIVE DATENANALYSE, die durch verschiedene Diagrammvisualisierungen dargestellt werden. Vorab wird aber ein DataFrame names "rating" für die Bewertungen erstellt mit Berücksichtigung der durchschnittlichen Bewertung und der Anzahl an Bewertungen**
rating=pd.DataFrame(df.groupby("title")["rating"].mean())
rating["num of ratings"] = pd.DataFrame(df.groupby("title")["rating"].count())
rating.head()
**Es sollte jetzt eine Liste zusehen sein. In den Zeilen sind die ersten 5 Filme zusehen. In der ersten Spalte ist die durchschnittliche Bewertung und in der zweiten Spalte die Anzahl an Bewertungen des jeweiligen Films zusehen (z. B."Til There Was You (1997), rating = 2,3333, num of rating = 9).**

**Erstellung eines Histogramm:**
plt.figure(figsize=(10,4))
rating["num of ratings"].hist(bins=70)
**Durch Ausführen der Befehle wird ein Histogramm (Balkendiagramm) erstellt, wo die Verteilung der Bewertungsanzahl zusehen ist.

**Im nächsten Schritt wird eine Empfehlung erstellt. Zunächst wird eine neue Matrix erstellt:**
movie_mat = df.pivot_table(index="user_id",columns="title",values="rating")
movie_mat.head()
**Nun wird ein neues DataFrame angezeigt, da wir das alte DataFrame pivotiert haben. In den Spalten befinden sich die Filmtitel und der Index ist nun die user_id. Die Werte sind die ratings. Es sind viele NaN Werte zusehen, da nicht jeder User zu jedem Film eine Bewertung abgegeben hat.**

**Es werden nun zwei Filme gewählt (Starwars (1997) und LiarLiar(1997)) auf denen die Empfehlungen basieren und erstellen eine separate Liste mit den User Ratings:**
starwars_user_ratings=movie_mat["Star Wars (1977)"]
liarliar_user_ratings=movie_mat["Liar Liar (1997)"]
starwars_user_ratings.head()
**Durch Ausführen des .head() ist von jeder einzelnen user_id die Bewertung zusehen (NaN=keine Bewertung abgegeben). Das selbe sollte auch für den anderen Film LiarLiar zusehen sein, wenn liarliar_user_ratings.head() ausgeführt wird.**
similar_to_starwars=movie_mat.corrwith(starwars_user_ratings)
similar_to_liarliar=movie_mat.corrwith(liarliar_user_ratings)
similar_to_starwars.head()
**Es werden neue Listen erstellt, um die Korrelationen der ausgewählten Filme zu den anderen filem herauszufinden. Mit corrwith werden die Korrelationen zwischen dem gewählten Film, z. B. Starwars, zu allen anderen Filmen auf Basis der ratings berechnet. Dies wird durch ausführen mit .head() zusehen sein. Für eine saubere Darstellung, werden die Ergebnisse in einem neuen DataFrame gespeichert und alle NaN-Werte entfernt (im Beispiel hier wird es für Starwars durchgeführt):**
corr_starwars = pd.DataFrame(similar_to_starwars,columns=["Correlation"])
corr_starwars.dropna(inplace=True)
corr_starwars.head()

**Um ein sinnvolles Ergebnis für die Korrelation zu erhalten, werden nur Filme betrachtet, die mehr als 100 Bewertungen haben. Dazu werden folgenden Befehle durchgeführt:**
corr_starwars=corr_starwars.join(rating["num of ratings"])
corr_starwars[corr_starwars["num of ratings"]>100].sort_values("Correlation",ascending=False).head(10)
**Im DataFrame corr_starwars wird die Spalte "num of ratigns" aus dem DataFrame rating hinzugefügt. Daraufhin wird durch die Bedingung "corr_starwars["num of ratings"]>100" alles Filme mit mehr als 100 Bewertungen betrachtet und die Korrelationen mit absteigenden Wert angezeigt.

**Um die Empfehlungen für den Film Liarliar zu erhalten, muss folgendes ausgeführt werden:**
corr_liarliar = pd.DataFrame(similar_to_liarliar,columns=["Correlation"])
corr_liarliar.dropna(inplace=True)
corr_liarliar=corr_liarliar.join(rating["num of ratings"])
corr_liarliar[corr_liarliar["num of ratings"]>100].sort_values("Correlation",ascending=False).head(10)


