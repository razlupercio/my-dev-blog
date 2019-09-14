---
layout: post
title: javascript programming assessment
categories: [javascript]
tags: [dev, challenges]
fullview: false
comments: true
---
some days ago i was cleaning my files and found this assessment i did for a job interview process, i remember that i was quite satisfied with the results, even though i did it in only 1 hour after procastinating all the weekend!

however after reviewing this code it doesn't fullfil my expectatives, time make us wisers! so, maybe i'll tune up this code a bit since i'm on a hipe for javascript lately. 

these are the assessment

## Instructions:

* You can use any open source libraries; build tools, test tools etc.
* Think about the edge cases and ensure the code you write is robust and well tested.
* Include a readme file which has instructions for building and running the code.
* The solution presented should be in JavaScript and may include other web elements images, css and html.

### Part 1:
Implement the logic for a vending machine that will return the optimal change for a given number of Euro. The method should have the signature and return a list/array of coins: `getOptimalChangeFor(euro)`

* Assume an unlimited supply of coins.
* There are no restrictions on Coin object except that it must contain the property denomination
* This method should return the least number of coins possible
* The following denominations of coin are to be used
    + OneEuro=100
    + Fiftycents=50
    + Twentycents=20 
    + Tencents=10
    + Fivecents=5
    + Twocents=2
    + Onecent=1

### Part 2:
Implement a method to get the change for a given number of Euro based on a limited supply of coins. The method should have the signature and return a list/array of coins: `getChangeFor(euro)`

* This method should return the least number of coins possible as long as they are available in the inventory.
* When the method is invoked, the number of coins left in the inventory should be reduced.
* Throw an exception if there is insufficient coinage.
* Refactor and reuse code from Part 1 where possible.
* The initial configuration of coins available are shown below:

|Euro cents     | Number of coins  |
|---|---|
| 100  | 11  |
| 50 | 24  |
| 20  | 0  |
| 10  | 99  |
| 5  | 200  |
| 2  | 11  |
| 1  | 23  |

# Solution (WIP)

first of all i set an array that holds the possible denominations of coins:

{% highlight javascript %}
var coins = [100, 50, 20, 10, 5, 2, 1];
{% endhighlight %}

then i wrote a function that validates the input if not it gets discarded **spoiler ahedad!!** it doesn't! you can add an input like 6.40+10 and it will be accepted.

{% highlight javascript %}
function validateDecimal(value)    {
    var RE = new RegExp(/^\d+(?:\.\d{1,2})?$/);
    if(RE.test(value)){
       return true;
    }else{
       return false;
    }
}
{% endhighlight %}

and this is the functions that performs the magic, it will get the decimal portion to 

{% highlight javascript %}
var programmingAssesment = {
    getOptimalChangeFor : function(amount){
        var coinsArray=[];
        if(validateDecimal(amount)){
            amount = amount * 100;
            for(var i = 0; i < coins.length;i++){        
                var coins_m = Math.floor(amount/coins[i]);
                amount = amount % coins[i];
                coinsArray.unshift(coins_m);
             }
        return coinsArray           
    } else  {
            return "Format not valid."
        }
    }
}
{% endhighlight %}





