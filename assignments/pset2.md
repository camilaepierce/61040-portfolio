# Problem Set 2

## Concept Questions
1. The contexts are for each of the services that use the URL shortening service, or notably the base of the URL for which the URLs are being made. This allows for different services to use shortened strings that have already been used in other services, but not within its own.
2. The NonceGeneration must store sets of used strings in order to keep track of which shortened URLs have already been used, in order to not create the same shortened string twice. In the provided case, the set of used strings in the spec is related to the counter in the implementation because it takes in both the number of the count and the entire set of Shortenings that are maintained by the UrlShortening concept, and returns a new shortening string, updating the counter number. In practice, this would be discouraged, as it requires the inner workings of a separate concept to be conveyed publically, disrespecting the rep exposure principle.
3. Advantage: An advantage from the perspective of the user is that the common dictionary words are easy to remember and more difficult to mispell, and will allow for easy access to the shortened URL, helping their own use case.
Disadvantage: There is a limited number of 'common dictionary words', and they might run out of words before the links expire. In order to realize this idea, the NonceGeneration would require modification to the `generate` function, requiring an optional parameter, `word` to be added. If the choice is a valid new shortening then the nonce is returned. Otherwise, the word could be appended with a number or some other slight modification that would still allow it to be used.

## Synchronization Questions
1. This is because in the first sync, generate, the `then` action of `NonceGeneration.generate()` requires the argument of only the context, so only the `shortUrlBase` needs to be explicitly outlined within the `when` clause. However, in register, `UrlShortening.register` requires the `shortUrlSuffix`, `shortUrlBase`, and `targetUrl` as parameters. This means that they must be described within the `then` clause.
2. This convention isn't used in cases when either a specific amount needs to be prespecified (as in the seconds) or when the name is carried over from the when `when` clause in order to maintain continuity, and clearly describe how the functions should be synced together.
3. The request action is included in the first two syncs but not the third because the third, setting expiry, can only be completed after a shortened URL has actually been created. This is why the third action follows the completion of the UrlShortening.register, and provides the finalized shortened URL. The first two syncs require information from the Request.shortenUrl() action.
4. If operating within a fixed domain, the synchonization would not need to convey the shortUrlBase information across requests, since the fixed domain would be standardized throughout the available actions.
5. Adding a sync for when a resource expires:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**sync** expireResource   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**when** ExpiringResource.expireResource(): resource   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**then** UrlShortening.delete(shortUrl)  


Additionally, another action would need to be added to NonceGeneration in order to remove strings from the set of used strings, in order for them to be used again after a resources has expired.

## Extending the Design
1. Additional concepts.

&nbsp;&nbsp;&nbsp;&nbsp;**concept** UserAuthentication  
&nbsp;&nbsp;&nbsp;&nbsp;**state**  
&nbsp;&nbsp;&nbsp;&nbsp;a set of Users with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a username String  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a password String  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a set of Shortenings  
&nbsp;&nbsp;&nbsp;&nbsp;**actions**  
&nbsp;&nbsp;&nbsp;&nbsp;createUser(username: String, password: String)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** username not taken within set of Users  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a User with username, password, and an empty set of Shortenings  
&nbsp;&nbsp;&nbsp;&nbsp;authenticate(usrnm: String, psswrd: String): User  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** usrnm and matching pssword within set of Users  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns User, including set of associated Shortenings  

&nbsp;&nbsp;&nbsp;&nbsp;**concept** LinkAnalytics   
&nbsp;&nbsp;&nbsp;&nbsp;**state**  
&nbsp;&nbsp;&nbsp;&nbsp;a set of ShortURLs with  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a count of numClicks  
&nbsp;&nbsp;&nbsp;&nbsp;**actions**  
&nbsp;&nbsp;&nbsp;&nbsp;linkClicked(short: shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** shortening is a valid shortening  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effect** increments the counter of numClicks for this shortening by 1  
&nbsp;&nbsp;&nbsp;&nbsp;deleteCount(short: shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** shortening exists within the set   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effect** removes shortening and associated numClicks from the set  
&nbsp;&nbsp;&nbsp;&nbsp;initialReg(short: shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** shortening does not already exist within state  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effect** adds shortening to set of Shortenings for click tracking  
&nbsp;&nbsp;&nbsp;&nbsp;viewAnalytics(short: shortUrl): numClicks  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requries** shortUrl exists  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns number of clicks the shortUrl has  


2. Essential synchronizations.  
&nbsp;&nbsp;&nbsp;&nbsp;1. Shortenings are created  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**sync** startAnalytics  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**when** UrlShortening.register(): shortUrl  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**then** LinkAnalytics.initialReg(shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;2. Shortenings are translated to targets  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**sync** linkAccess  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**when** UrlShortening.lookup(shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**then** LinkAnalytics.linkClicked(shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;3. User examines analytics  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**sync** examineAnalytics  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**when** UserAuthentication.createUser(username, password); Request.viewAnalytics(shortUrl)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**then** UserAuthentication.authenticate(username, password); LinkAnalytics.viewAnalytics(shortUrl): numClicks  
3. Additional feature requests.  
    - Allowing users to choose their own short URLs;  
        This would be undesirable, since a user choosing their own URL might lie outside of the domain that the ShortnedURL gneration has control over, and could not guarantee independence or unique URL/redirections.  
    - Using the “word as nonce” strategy to generate more memorable short URLs;   
        This would require minimal modification, as outlined in the Concept Questions above. An option would have to be provided to the user in order to submit their own word for a short URL generation. This would affect `generate` sync, as then the NonceGeneration.generate() action would not have to be called, since the user is already providing. However, the string the user chooses would still have to be added to the set of used strings, so the program can avoid reusing that string in future calls of the program.
    - Including the target URL in analytics, so that lookups of different short URLs can be grouped together when they refer to the same target URL;  
        This would require a modification to the examineAnalytics sync and the accessAnalytics concept, as instead of looking through the links by the shortened version, the search through the links could also be completed by looking through the full URLs (by requesting the full URL to also search by), and then returning all of the requested information.  
    - Generate short URLs that are not easily guessed;  
        This would not really be an issue that would be discussed within the design of the concept. This would likely lie closer to the implementation of the URL generation, with the heavy lifting coming from the randomization algorithm that is run to generate new links.  
    - Supporting reporting of analytics to creators of short URLs who have not registered as user.  
        This is also something that might lie better within the implmentation of the concept, such as adding a sharing feature, that exports the analytics information in some formatted way. If this was required to be added into teh concept specification, however, it could be included by adding some new user, named the GlobalUser, that does not require a password (or has an empty password), and does not require registration. Then, the Shortening could belong to multiple users, or all Shortenings of this nature could be automatically added to the GlobalUser, disregarding the need for user authentication. This in practice would not be the best idea, as it would be a very insecure system.  
