# Wiki-Content-Retriever

Given a text corpus, the script extracts the most important keywords and tries to retrieve content from wikipedia for each keyword. If the exact keywords is not present in wikipedia's search result then the next closest keyword is searched for and the content is retrieved.

- This script was developed as part of my ongoing project of building a local GPT powered study tool. Since wikiapi is a bit buggy and mediawiki is too confusing, I wanted a tool that can give me the paragraph content easily. 

## Requirements:
- `wikipedia 1.4.0`
- `spacy 3.6.1`
- `tqdm 4.66.1`

## Methods:
#### get_keywords:
- Returns a list of keywords extracted from the supplied text. Keywords are extracted using `spacy` - `en_core_web_sm` and `en_core_sci_lg` models.
- Experimented with yake and Rake-NLTK but wasn't satisfied with them.

#### wiki:
- Returns the content from Wikipedia based on the given keyword/word. I'm using a hacky way of finding the closest keyword if the original keywords do not exist on Wikipedia by using `wikipedia.search()` and `api.wikimedia.org/core/v1/wikipedia/en/search/page` to verify.
- Request is sent to `en.wikipedia.org/wiki/{MATCHED_KEYWORD}`  and the content within the paragraph tag is extracted via BeautifulSoup and cleaned with regex. The number of paragraphs to be included can be specified.

#### get_details:
- Combines `get_keywords` and `wiki` methods to return a dictionary of multiple keywords and their corresponding extracted content.

All of these methods can be used independently.  
Most of the edgecases such as empty keywords and search results have been handled.

## Sample:

```python
text="""Fraser and Neave, Limited (F&N) is a Thai-Singaporean food and beverage, publishing, brewing and property industries conglomerate.
It is owned by Thai Chinese billionaire business magnate Charoen Sirivadhanabhakdi."""

response=get_details(text, medical = False)
response
```
```
  0%|                                                                                                                                                                 | 0/7 [00:00<?, ?it/s]
Fraser
Fraser not found. Redirecting to FRASER
 14%|█████████████████████▊                                                                                                                                   | 1/7 [00:10<01:00, 10.13s/it]
 Limited
 Limited not found. Redirecting to The_Limited
 29%|███████████████████████████████████████████▋                                                                                                             | 2/7 [00:14<00:33,  6.61s/it]
 Charoen Sirivadhanabhakdi
 Charoen Sirivadhanabhakdi not found. Redirecting to Charoen_Sirivadhanabhakdi
 43%|█████████████████████████████████████████████████████████████████▌                                                                                       | 3/7 [00:18<00:22,  5.54s/it]
 F&N
 F&N not found. Redirecting to Fraser_and_Neave
 57%|███████████████████████████████████████████████████████████████████████████████████████▍                                                                 | 4/7 [00:22<00:14,  4.75s/it]
 Chinese
 Chinese not found. Redirecting to China
 71%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████▎                                           | 5/7 [00:27<00:10,  5.17s/it]
 Neave
 Neave not found. Redirecting to Airey_Neave
 86%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏                     | 6/7 [00:34<00:05,  5.50s/it]
 Thai
 Thai not found. Redirecting to Thailand
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 7/7 [00:38<00:00,  5.51s/it]
```
```
({'Fraser': ['The Federal Reserve Archival System for Economic Research (FRASER) is a digital archive begun in 2004 by the Federal Reserve Bank of St. Louis to safeguard, preserve and provide easy access to the United States’ economic history, particularly the history of the Federal Reserve System, through digitization of documents related to the U.S. financial system.',
   'Digitized documents include:',
   'FRASER in 2016 added:. The Commercial and Financial Chronical (over 2000 issues in 68 volumes, mostly from 1871-1935)..The Federal Reserve Bank of St. Louis Financial Crisis Timeline and its original supporting documents, which includes a comprehensive collection of documents from the 2007-09 financial crisis.. The Robert Owen papers. OKlahoma\'s Senator Owen served from 1907-25, and was a co-sponsor of the Federal Reserve Act of 1913.. Publications and speeches from several Federal Reserve Banks.. FRASER also debuted a new "African Americans in the Economy" theme, featuring materials from the Department of Labor\'s Division of Negro Economics (1917-25).'],
  ' Limited': ['',
   'The Limited is an American clothing brand sold exclusively through Belk. The Limited began with operating retail stores between the early 1960s and the late 2010s. In 2017, it became a brand owned by the private equity firm Sycamore Partners.',
   'Bella Cabakoff was born in Williamsburg, Brooklyn and moved to Columbus, Ohio as a toddler. At 21, she became the youngest buyer for the Lazarus department store chain. In 1951, after spending over 20 years with Lazarus, she and her husband Harry Wexner opened a women\'s clothing store named Leslie\'s (after their son) on State Street. This store became the training ground for Leslie Wexner. In 1963, he borrowed $5,000 from his aunt and $5,000 from the bank and opened a store at the Kingsdale Shopping Center in Upper Arlington. This store was named "The Limited" because the store focused on clothing for younger women, unlike his parents\' general merchandise store. Later in 1964, Bella and Harry closed their store to join their son in his venture.'],
  ' Charoen Sirivadhanabhakdi': ['',
   'Charoen Sirivadhanabhakdi (Thai: เจริญ สิริวัฒนภักดี; Chinese: 苏旭明; pinyin: Sū Xùmíng; RTGS: Charoen Siriwatthanaphakdi; born 2 May 1944) is a Thai business magnate and investor.',
   "He is the founder of Thai Beverage, and the chairman of conglomerates TCC Group and Fraser and Neave, Ltd (F&N). The Sirivadhanabhakdi family is now Thailand's largest property developer and landlord of 630,000 rai (101,000 ha; 250,000 acres), plus commercial and retail buildings in Singapore. He also owns 50 hotels in Asia, the US, UK, and Australia, including Plaza Athénée in Manhattan, New York City, US, and The Okura Prestige Bangkok. As of 2020, Forbes estimates his net worth at US$10.5 billion."],
  ' F&N': ['Fraser and Neave, Limited (F&N) is a Thai-Singaporean food and beverage, publishing, brewing and property industries conglomerate. It is owned by Thai Chinese billionaire business magnate Charoen Sirivadhanabhakdi.',
   "Listed in Singapore, the group's subsidiaries and associated companies include Frasers Property, Asia Pacific Breweries and Times Publishing. As of 2011, F&N had total assets of over S$14 billion and employed over 7,890 people in 11 countries.",
   'In January 2014, through a distribution in specie and re-listing of Frasers Centrepoint Limited by way of introduction on the Singapore stock exchange, the group de-merged its properties business.'],
  ' Chinese': ['',
   '',
   "China (Chinese: 中国; pinyin: Zhōngguó), officially the People's Republic of China (PRC), is a country in East Asia. It is the world's second-most populous country with a population exceeding 1.4 billion. China spans the equivalent of five time zones and borders fourteen countries by land, tied with Russia as having the most of any country in the world. With an area of nearly 9.6 million square kilometres (3,700,000 sq mi), it is the world's third largest country by total land area. The country is divided into 22 provinces, five autonomous regions, four municipalities, and two semi-autonomous special administrative regions. The national capital is Beijing, and the most populous city and largest financial center is Shanghai."],
  ' Neave': ['',
   'Lieutenant Colonel Airey Middleton Sheffield Neave, DSO, OBE, MC, TD (/ˈɛəri ˈniːv/) (23 January 1916 – 30 March 1979) was a British soldier, lawyer and Member of Parliament (MP) from 1953 until his assassination in 1979.',
   'During World War II he was the first British prisoner-of-war to succeed in escaping from Oflag IV-C at Colditz Castle, and later worked for MI9. After the war he served with the International Military Tribunal at the Nuremberg trials. He later became Conservative MP for Abingdon.'],
  ' Thai': ['',
   '– in Asia (grey)– in ASEAN (grey)',
   "Thailand, officially the Kingdom of Thailand and historically known as Siam, is a country in Southeast Asia on the Indochinese Peninsula. With a population of almost 70 million, it spans 513,120 square kilometres (198,120 sq mi). Thailand is bordered to the north by Myanmar and Laos, to the east by Laos and Cambodia, to the south by the Gulf of Thailand and Malaysia, and to the west by the Andaman Sea; it also shares maritime borders with Vietnam to the southeast, and Indonesia and India to the southwest. Bangkok is the nation's capital and largest city."]},
 {' Charoen Sirivadhanabhakdi',
  ' Chinese',
  ' F&N',
  ' Limited',
  ' Neave',
  ' Thai',
  'Fraser'})
```
- Here, `limit=3`. It can be increased to include the entire content present on wikipedia

## Usecase:
Can be used for general information provision, fact checking or to even create a large knowledge base with respect to the provided text, in turn creating datasets for training.
