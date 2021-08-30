DEVELOPMENT Version

# Clustering of NBA Players in 2019  
It's been understood around the NBA that there are many players in the National Basketball Association (NBA) that don't currently fit the traditional player types of the point guard, shooting guard, power forward, small forward, and center. Some of these players have been around the league for awhile such as LeBron James (what can the man not do?) and Pau Gasol (a center that is a decent 3 point shooter). Others are newer such as Nikola Jokic, who is also a well known center that is a great 3 point shooter, or even Steph Curry, a point guard that seemingly launches 3 point shots from anywhere on the court.

My goal was to be able to reorganize the players into modern NBA categories; however, I did not exactly know what those categories would be. I decided to take an unsupervised approach to clustering and the resulting ETL, analysis, and modeling can be found in `nbaPlayerClassify.ipynb`

## Data

Data was obtained from `www.basketball-reference.com`, specifically statistics from the 2018-2019 NBA season. Because we are classifying players, I obtained player stats from 2 tables - Total Stats and Advanced Stats. I also scraped the stats from the tables instead of simply using csv's that could be downloaded from the site to add a bit of a challenge. As a result, utilizing the `BeautifulSoup` library in Python, I was able to scrape the statistics based on the HTML layout of the site. Stats are stored in a table format on the site, specifically with the `<th>` header. Code wise, this looks something like this: `[th.getText() for th in soup.findAll('tr', limit=2)[0].findAll('th')]` to get the right headers, aka locating the table, and `[[td.getText() for td in rows[i].findAll('td')]
            for i in range(len(rows))]` to get the specific stats where `rows` is finding all of the `<tr>` elements.  

After joining the two tables using `pandas.merge`, I noticed that there were a few blank columns as well as some duplicate rows that needed to be dropped. I removed some variables that I decided would not be of importance for our analysis - Age, Team, and Position. I especially wanted to remove Position because I didn't want the algorithm to be biased on the current position a player plays. Rather, I wanted it to find patterns based on statistics players produce over the course of the NBA season to define which cluster they are placed in.  

Once the data was clean, I was ready to proceed to the next part - prepping for the model.  

## Principal Components Analysis (PCA)  

I elected to use Principal Components Analysis (PCA) because intuitively, I knew some of the features would potentially be highly correlated with each other. I skipped building a correlation matrix because intuitively the points you score will be highly correlated with the number of 2 point shots you make or 3 point shots you make.  
With PCA, you can only feed in numeric data so I had to make sure in my data cleaning to convert numeric data classified initially as objects into either int64 or float64 type. I also verified that there were no missing values in the data once again before running PCA.  

PCA in Python works by first selecting a scaler and then scaling the data. Once the data has been rescaled, we can apply PCA, which will eventually tell us the `n_components` to use in further analysis. Technically, you select the optimal n_components by looking at a table of outputs from applying PCA showing the component, explained variance, and cumulative explained variance. You want to select n_components that explains 90% of the cumulative variance.

## K-Means Clustering  

Now is the time to cluster using the K-Means Clustering Algorithm. In order to find the number of clusters to use, I utilized the Elbow Method for finding the optimum "k." My resulting graph was a bit ambiguous to read so I utilized something called the Kneedle Algorithm in order to mathematically find the optimal number of clusters. Running the K-Means with the optimal number of clusters on the result from the PCA gives us a dataframe with the cluster label attached to the features of the corresponding centroid. The key is that when we go back to our original dataset and run it through the K-Means Algorithm, we end up sorting each NBA player into the appropriate cluster based on which centroid it best matches.  
## Results  

We can examine each cluster's players, but we have to interpret what the cluster is made of. For example, one cluster I had contained primarily centers and power forwards. Furthermore, I noticed that they have rather high Field Goal (FG) percentages, meaning that they're likely traditional "Big Men" that stay around and score mostly from the post. These players include Andre Drummond, Clint Capela, DeAndre Jordan, Enes Kanter, and Hassan Whiteside.

I also found another more interesting cluster made up of lots of different roles. The players in this cluster include Damien Lillard, Kevin Durant, Lebron James, and Stephen Curry. I named this group the "floor general group" because these players are high scoring players that are also usually players that start the offensive play.  

With more time, I'd like to further explore each of the clusters to see if my interpretations make sense. For the time being, what I've been able to demonstrate is that instead of 5 traditional positions that define types of players in the NBA, the reality is that there are actually 8 player types in the NBA. An NBA team can use the resulting clusters, along with a deeper understanding of what defines the players in the cluster, to select the right player based on their offensive and defensive scheme and hopefully help them win a championship.  
