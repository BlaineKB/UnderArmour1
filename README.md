# UnderArmour1
# Grap multiple user's user_timeline from twitter API and save to Excel 
2 # Code will be save user's tweet ID, created Time, Coordinates-x, Coordinates-y, source, text. Can be modified at line 48 and so on 
3 # Original code from https://gist.github.com/yanofsky/5436496  "A script to download all of a user's tweets into a csv" 
4 
 
5 import xlsxwriter 
6 import tweepy  
7 
 
8 #https://github.com/tweepy/tweepy 
9 
 
10 consumer_key = "Your_consumer_key" 
11 consumer_secret = "Your_consumer_secret" 
12 access_key = "Your_access_key" 
13 access_secret = "Your_access_secret" 
14 
 
15 def get_all_tweets(screen_name): 
16 
 
17     auth = tweepy.OAuthHandler(consumer_key, consumer_secret) 
18     auth.set_access_token(access_key, access_secret) 
19     api = tweepy.API(auth) 
20 
 
21     alltweets = []   
22     new_tweets = [] 
23     outtweets = [] 
24 
 
25     new_tweets = api.user_timeline(screen_name = screen_name,count=200) 
26 
 
27     alltweets.extend(new_tweets) 
28 
 
29 	#save the id of the oldest tweet less one 
30     oldest = alltweets[-1].id - 1 
31 
 
32     #keep grabbing tweets until there are no tweets left to grab 
33     while len(new_tweets) > 0: 
34         print "getting tweets before %s" % (oldest) 
35 
 
36         #all subsiquent requests use the max_id param to prevent duplicates 
37         new_tweets = api.user_timeline(screen_name = screen_name,count=200,max_id=oldest) 
38 
 
39         #save most recent tweets 
40         alltweets.extend(new_tweets) 
41 
 
42         #update the id of the oldest tweet less one 
43         oldest = alltweets[-1].id - 1 
44 
 
45         print "...%s tweets downloaded so far" % (len(alltweets)) 
46 
 
47     #transform the tweepy tweets into a 2D array 
48     outtweets = [[tweet.id_str, tweet.created_at, tweet.coordinates,tweet.geo,tweet.source,tweet.text] for tweet in alltweets] 
49 
 
50     return outtweets 
51 
 
52 def write_worksheet(twitter_name): 
53 
 
54 	#formating for excel 
55 	format01 = workbook.add_format() 
56 	format02 = workbook.add_format() 
57 	format03 = workbook.add_format() 
58 	format04 = workbook.add_format() 
59 	format01.set_align('center') 
60 	format01.set_align('vcenter') 
61 	format02.set_align('center') 
62 	format02.set_align('vcenter') 
63 	format03.set_align('center') 
64 	format03.set_align('vcenter') 
65 	format03.set_bold() 
66 	format04.set_align('vcenter') 
67 	format04.set_text_wrap() 
68 
 
69 	out1 = [] 
70 	header = ["id","created_at","coordinates-x","coordinates-y","source","text"] 
71 
 
72 	worksheet = workbook.add_worksheet(twitter_name) 
73 
 
74 	out1 = get_all_tweets(twitter_name) 
75 	row = 0 
76 	col = 0 
77 
 
78 	worksheet.set_column('A:A', 20) 
79 	worksheet.set_column('B:B', 18) 
80 	worksheet.set_column('C:C', 13) 
81 	worksheet.set_column('D:D', 13) 
82 	worksheet.set_column('E:E', 20) 
83 	worksheet.set_column('F:F', 120) 
84 
 
85 	for h_item in header: 
86 		worksheet.write(row, col, h_item, format03) 
87 		col = col + 1 
88 
 
89 	row += 1 
90 	col = 0 
91 	 
92 	for o_item in out1: 
93 		write = [] 
94 		cord1 = 0 
95 		cord2 = 0 
96 		write = [o_item[0], o_item[1], o_item[4], o_item[5]] 
97 
 
98 		if o_item[2]: 
99 			cord1 = o_item[2]['coordinates'][0] 
100 			cord2 = o_item[2]['coordinates'][1] 
101 		else: 
102 			cord1 = "" 
103 			cord2 = "" 
104 
 
105 		format01.set_num_format('yyyy/mm/dd hh:mm:ss') 
106 		worksheet.write(row, 0, write[0], format02) 
107 		worksheet.write(row, 1, write[1], format01) 
108 		worksheet.write(row, 2, cord1, format02) 
109 		worksheet.write(row, 3, cord2, format02) 
110 		worksheet.write(row, 4, write[2], format02) 
111 		worksheet.write(row, 5, write[3], format04) 
112 		row += 1 
113 		col = 0 
114 
 
115 workbook = xlsxwriter.Workbook('Twitter_timeline.xlsx') 
116 
 
117 
 
118 write_worksheet('twitterID1') 
119 write_worksheet('twitterID2') 
120 
 
121 workbook.close() 
