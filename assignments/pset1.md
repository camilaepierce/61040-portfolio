# Problem Set 1

## Exercise 1: Reading a Concept
1. The first invariant of the state is that counts of items can only be nonnegative. The second invariant of the state is that purchases cannot be made until after a request has been made. The more important invariant here is the second, because this prevents users from purchasing random items "from the registry" which subverts the need for a registry at all, and having too many of a desired item is not necesarily detrimental (depending on the item). The action whose design is most affected by it is `purchase`, who preserves this invariant by only allowing purchase for items that have been requested.
2. `removeItem` would potentially break this invariant, since it does not check to see if anyone has purchased this item. In practice, this would be an issue if an item was requested, some number was purchased, then the item was removed from the registry. This would leave the Owner left with those items that in the end they did not want. This problem would be fixed by checking if any item has been purchased, and only removing from the registry if no purchases have been made. Alternatively, the Owner could decrease the count of remaining item requests to zero, so no more new purchases are made for that item.
3. A registry can be opened, then closed, then opened again any number of times. A reason to allow this would be for someone to re-use the registry several times. Another similar reason would be to close it, edit it, then open it again. This would allow for a user to completely update the registry without any purchases interfering with the registry in the meantime.
4. In practice, this wouldn't really matter. Instead of deleting a registry, an Owner can just close it and keep it closed permenantly. Comitting the further action of deleting the registry would not significantly change the functionality of the registry system.
5. One query that is likely to be executed against the aginst the concept state by a registry owner would be adding items to their registry. One query that is likely to be executed against the concept state by a giver would be purchasing items off of the registry.
6. This would likely need to be a spec change during `create`, so a Registry owner can decide if they want the purchases to be a surpise or not. Not much of the rest of the spec would likely need to be changed, other than maybe the purchases being revealed upon closure of the registry.
7. This is preferable to representing items with specific descriptors by allowing for the spec to be ready for change. This way is significantly more versatile, and can be applied to many types of items that may are may not have SKU codes, such as a different sorting system, or hyperlinks to site listings, etc. 


## Exercise 2: Extending a Familiar Concept
  **concept** PasswordAuthentication  
  **purpose** limit access to known users  
  **principle** after a user registers with a username and a password,  
&nbsp;&nbsp;&nbsp;&nbsp;they can authenticate with that same username and password  
&nbsp;&nbsp;&nbsp;&nbsp;and be treated each time as the same user  
  **state**  
&nbsp;&nbsp;&nbsp;&nbsp;a set of Users with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a Username  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a Password  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a secret Token  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a flag of Confirmation  
  **actions**  
&nbsp;&nbsp;&nbsp;&nbsp;register (username: String, password: String, email: String): (user: User)  
&nbsp;&nbsp;&nbsp;&nbsp;  **requires** username does not already exist  
&nbsp;&nbsp;&nbsp;&nbsp;  **effects** creates a new User with this username and password, generates and sends the secure token to the email, and flags this User as unconfirmed  
&nbsp;&nbsp;&nbsp;&nbsp;authenticate (username: String, password: String): (user: User)  
&nbsp;&nbsp;&nbsp;&nbsp;  **requires** a User with the matching username and password exist  
&nbsp;&nbsp;&nbsp;&nbsp;  **effects** returns the matching User  
&nbsp;&nbsp;&nbsp;&nbsp;confirm (username: String, token: String): (user: User)  
&nbsp;&nbsp;&nbsp;&nbsp;  **requires** username is within the set of Users, token matches the secret Token  
&nbsp;&nbsp;&nbsp;&nbsp;  **effects** User's flag is now set to confirmed, the registration is complete  

## Exercise 3: Comparing Concepts
  **concept** PersonalAccessTokens  
  **purpose** limit access to known users, and limit the access of known users to specific repositories
  **principle** after a user registers with a name, password, and email,
&nbsp;&nbsp;&nbsp;&nbsp; they (or their company or organization) can create tokens that can limit a user's access to specific or a range of repositories, according to a specified scope and time period in which the token will be active
  **state**  
&nbsp;&nbsp;&nbsp;&nbsp; a set of Users with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  an email
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a username
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a password
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a set of Tokens
&nbsp;&nbsp;&nbsp;&nbsp; a set of Tokens with
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a description
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a expiration
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a set of accessible repos Scope
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a token-string
&nbsp;&nbsp;&nbsp;&nbsp; a set of Repositories with
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a Name
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a Url
  **actions**  
&nbsp;&nbsp;&nbsp;&nbsp; register (username: String, password: String, email: String): (user: User)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** username does not already exist  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns a user with username, password, and an empty set of tokens  
&nbsp;&nbsp;&nbsp;&nbsp; createToken (user: User, scope: Scope, expiration: Datetime): (token: Token)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** user exists, expiration is in the future  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns a token-string with given scope and expiration  
&nbsp;&nbsp;&nbsp;&nbsp; authenticate (username: String, token: Token, url: String): (repo: Repository)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** a User with the matching username and token exist, and the token allows access to the repo  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns the requested repository  
&nbsp;&nbsp;&nbsp;&nbsp; deleteToken (user: User, token: Token)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** user exists, token belongs to user and exists  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** deletes token from user's collection  

I think that PersonalAccessToken differs from PasswordAuthentication because it focuses much more so delineating access to resources, as well as based on a time period. Although in terms of conveying an understanding of how to use tokens, the GitHub page does fairly well. However, tokens are much more directed at businesses or organizations who want to restrict access to certain repositories, or for developers to grant token usages for third parties and want to increase secureness of their resource, which is not made necessarily clear within the GitHub page. Some more information on use cases could improve the page.  


## Exercise 4: Defining Familiar Concepts  
  
URL Shortener  
  **concept** URLShortener    
  **purpose** shorten the length of URL's to make them more readable and easy to use  
  **principle** after a user finds a URL they would like to be shortened, they submit their long URL, and can either decide to use the given, randomized URL, or to create a meaningful short URL  
  **state**    
&nbsp;&nbsp;&nbsp;&nbsp; a set of ShortURLs with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a long URL  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a short URL  
  **actions**    
&nbsp;&nbsp;&nbsp;&nbsp; createRandom (long: String): ShortURL  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns a new ShortURL with a short URL that is distinct from any previously created, and the long URL  
&nbsp;&nbsp;&nbsp;&nbsp; createChosen (long: String, short: String): ShortURL  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** short not already a short URL in the set of ShortURLs  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns a new short URL that is the same as the user-provided short  
&nbsp;&nbsp;&nbsp;&nbsp; delete (url: ShortURL): (token: Token)    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** url exists  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** short URL no longer redirects to the old URL, is now unlisted  


Time-Based One-Time Password (TOTP)  
  **concept** TimedOncePassword    
  **purpose** allow for secure logins with secondary device  
  **principle** after logging in with a username and password, a user must authenticate with a secondary step, which is inputting a temporary code that was sent to a trusted device  
  **state**  
&nbsp;&nbsp;&nbsp;&nbsp; a set of Tokens with
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a send-to Contact  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a authenticator  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a tokenMessage  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  an expiration Time    
  **actions**    
&nbsp;&nbsp;&nbsp;&nbsp; createToken (contact: String, time: Datetime): (token: Token)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** sends a tokenMessage to the contact, and returns the authenticator information  
&nbsp;&nbsp;&nbsp;&nbsp; authenticate (token: Token, tokenMessage): (authenticated: Boolean)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** token exists  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** if the token matches the authenticator, and the token has not expired, then return true, otherwise, false  
&nbsp;&nbsp;&nbsp;&nbsp; delete (token: Token)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** token has expired  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** removes token from set of tokens, no longer functions as a one-time password  
This concept improves security by adding a secondary barrier to the login process, spreads out the responsibility of a user perhaps having a simple and easy-to-guess password. However, this also means that if an attacker has access to a user's primary authentication device, that attacker has a large range of access to whatever accounts use just the One Time Password system to log in, or even to reset passwords.  

Conference Room Booking  
  **concept** BookRoom    
  **purpose** streamline the room booking process  
  **principle** after a user finds a URL they would like to be shortened, they submit their long URL, and can either decide to use the given, randomized URL, or to create a meaningful short URL  
  **state**    
&nbsp;&nbsp;&nbsp;&nbsp; a set of Bookings with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a User   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a Room  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a Time  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a Duration  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a Title  
&nbsp;&nbsp;&nbsp;&nbsp; a set of Rooms with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a location  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  a set of allowed Times  
  **actions**    
&nbsp;&nbsp;&nbsp;&nbsp; createBooking (user: User, room: Room, time: Datetime, duration: Number, title: String): (booking: Booking)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** user is a member, room exists and has no time conflicts with other bookings, and time is within room schedule  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a booking with given information and adds to set of Bookings  
&nbsp;&nbsp;&nbsp;&nbsp; deleteBooking (user: User, booking : Booking)   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** user is the user within the booking, booking exists  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** removes the booking from the set of bookings  
&nbsp;&nbsp;&nbsp;&nbsp; makeRoomAvailable (location: String, times: {Datetimes}): (room: Room)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** Datetimes do not overlap  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** adds available room to Rooms, with location and times  
&nbsp;&nbsp;&nbsp;&nbsp; removeRoom (room: Room)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** room exists  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** removes room from set of available rooms, deletes bookings of this room, and notifies users of change  

A nuance here is how to remove rooms that have been booked for a particular time slot. However, this is an action that only administrators would be able to access, and they should have the power to kick any user out of the room, especially if the room will no longer be available.