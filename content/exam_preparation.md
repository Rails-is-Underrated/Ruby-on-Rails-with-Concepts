# Exam Preparation

### Movie Rating
![alt avatar-the-last-airbender-the-promise-part-1](/images/avatar-the-last-airbender-the-promise-part-1.jpg)
You are to rate a movie/series every time you watch it. Each time you watch it again, you rate it again.
For example, you watched **Avatar: The Last Airbender**, and then you initially rated it as **1500**.
We illustrate this rating as a `[500, -1000, 2000]` list. Complete the function so it will return a `list` with two items on it; the first item is the sum of all ratings, and the second item is the highest rating of all time. The function should return `[3000, 2000]` in our example.

#### Explanation

1. Given an array of `[500, -1000, 2000]`, we add the initial rating of `1500`, so our final list is `[500, -1000, 2000, 1500]`
2. The sum of this list is `500 + (-1000) + 2000 + 1500 = 3000`, hence the first item on the list returned by the function
3. The highest rating on our final list is `2000` obviously; hence the second item on the list returned

*Note: You will be given a list of ratings and will have 1500 as the initial rating*

#### Hands on

1. Pull the latest changes from `main` branch
2. You'll see the sample codes on `files`, you should be able to see something like this
![alt exam_preparation_sample_code](/images/exam_preparation_sample_code.png)
1. On your terminal, within the project directory, run `pip install -U pytest` or `pip3 install -U pytest`
2. Then run `pytest`
3. You should be able to see similar to this
![alt exam_preparation_sample_code_2](/images/exam_preparation_sample_code_2.png)