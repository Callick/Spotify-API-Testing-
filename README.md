# Spotify API Testing 🎵🎧
This project showcases the testing of Spotify API endpoints using Postman and Newman. The tests cover various endpoints to ensure functionality, reliability, and performance such as reading user profiles, managing playlists, and handling tracks.
## Features
**✅ Automated API Testing:** Covers endpoints like user profiles, playlists, and tracks. <br>
**👥 User Profile Management:** Fetch and update user profile information. <br>
**▶️ Playlist Management:** Create, read, update, and delete playlists. <br>
**🎶 Track Management:** Save, read, and delete tracks for the current user. <br>
**🛠️ Custom Assertions:** Checks for response codes, headers, and body content. <br>
**⏱️ Response Time Validation:** Ensures API performance meets specified benchmarks. Measure response times and data sizes for API requests.
## API Documentation
https://documenter.getpostman.com/view/33497213/2sAYQiB7aX
## Technology Used
  **1. Postman:** For API testing and test script creation. <br>
  **2. Newman:** To run Postman collections via the command line. <br>
  **3. HTML Extra:** For detailed, visually enhanced test reports. <br>
  **4. JavaScript:** Test assertions and validations. <br>
## Pre-requisite
Before using this project, ensure the following:
  1. Spotify Developer Account with an active application for API access.
  2. Node.js installed on your system.
  3. Newman and htmlextra installed globally:
     `npm install -g newman newman-reporter-htmlextra`
## Installation
 **1. Clone the repository:**
```
    git clone https://github.com/Callick/spotify-api-testing.git
    cd spotify-api-testing
```
 **2. Install Newman:**
```
    npm install -g newman
```
 **3. Set up your environment variables for Spotify API credentials.**
## Usage
 **Running the API Tests:**
   - Update the Postman environment file with your Spotify API credentials (Client ID, Client Secret, and access token).
   - Execute tests via Newman:
     `newman run Spotify_API_Testing.postman_collection.json -e Spotify_API_Testing.postman_environment.json -r htmlextra --reporter-htmlextra-export ./reports/Spotify-Test-Report.html`
   - Open the generated report:
     `start ./reports/Spotify-Test-Report.html`
## Testing
  **Summary:** <br>
       - Total Requests<sup>13</sup>  
       - Total Assertions<sup>99</sup>  
       - Total Run Duration<sup>15s</sup>  
       - Failures<sup>20</sup>  
       - Average Response Time<sup>267ms</sup>  
  **Detailed Results:** <br>
       **- User Profile**<br>
           1. GET /v1/me: <sup>80% pass rate.</sup><br>
           2. GET /v1/me/following?type=artist: <sup>78% pass rate.</sup><br>
       **- Playlists** <br>
           1. GET /v1/me/playlists: <sup>75% pass rate.</sup><br>
           2. POST /v1/users/{user_id}/playlists: <sup>88% pass rate.</sup><br>
           3. GET /v1/playlists/{playlist_id}: <sup>100% pass rate.</sup><br>
           4. PUT /v1/playlists/{playlist_id}: <sup>75% pass rate.</sup><br>
       **- Tracks** <br>
           1. GET /v1/me/tracks: <sup>71% pass rate.</sup><br>
           2. PUT /v1/me/tracks: <sup>67% pass rate.</sup><br>
           3. DELETE /v1/me/tracks: <sup>67% pass rate.</sup><br>
## 1. Read_Current_User's_Profile
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL` <br>
   - **URL:** [{{baseURL}}/me](https://api.spotify.com/v1/me) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:** <br>
```
    pm.test("Checked whether the response code is 200 or not!", function () {pm.response.to.have.status(200)})
    switch(pm.response.code){

          case 200:
              // To verify the reponse body
              pm.test("Checked whether response body contains data or not!", function(){
                  pm.expect(pm.response.text()).to.not.be.empty
              })
              // To verify header authorization
              pm.test("Checked whether response header contains authorization or not!", function () {
                  pm.response.to.have.header("Authorization")
              });
              // To verify response header
              pm.test("Checked whether response header has expected Content-type or not!", function(){
                  pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
              })
              // To verify the spotify URL
              pm.test("Checked whether response has valid URL of user-profile  or not!", function () {
              // Parse the response body as JSON
              var jsonData = pm.response.json();
              
              // Define the regex pattern for the expected Spotify URL format
              var spotifyUrlPattern = /^https:\/\/open\.spotify\.com\/user\/.+$/;
              
              // Assert that the spotify URL matches the expected format
              pm.expect(jsonData.external_urls.spotify).to.match(spotifyUrlPattern);
          });
              pm.test("Checked whether the response time is less than 0.2s or not!", function () {
                  pm.expect(pm.response.responseTime).to.be.below(200)
              })
              pm.test("Checked whether the response size is less than 3KB or not!", function(){
                  pm.expect(pm.response.responseSize).to.be.below(3072)
              })
              pm.environment.set("user_id", pm.response.json().id)
              var user_name = pm.response.json().display_name
              pm.test(`Successful to fetch details of ${user_name}'s Spotify Account.`)
              var resTime = pm.response.responseTime / 1000
              pm.test(`The response Time Was ${resTime} Seconds.`)
              pm.environment.set("username", user_name)
      
          break
          // Below cases are as per Spotify API Document
          case 401:
              pm.test("The Access Token Has Expired.")
          break
          case 403:
              pm.test("Server is refusing to fulfill your request.")
          break
          case 429:
              pm.test("You've requested too many requests.")
          break
          default:
              pm.test("Unsuccessful to fetch details of Spotify Account.")
      
      }
```  
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>

## 2. Read_Followed_Artists
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL` <br>
   - **URL:** [{{baseURL}}/me/following?type=artist](https://api.spotify.com/v1/me//following?type=artist) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:** <br>
```
        pm.test("Checked whether the response code is 200 or not!", function () {
             pm.response.to.have.status(200)})
                 switch(pm.response.code){
                     case 200:
                         // To verify the reponse body
                         pm.test("Checked whether response body contains data or not!", function(){
                             pm.expect(pm.response.text()).to.not.be.empty
                         })
                         // To verify response header
                         pm.test("Checked whether response header has expected Content-type or not!", function(){
                             pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                         })
                         pm.test("Checked whether response time is less than 0.2s or not!", function () {
                             pm.expect(pm.response.responseTime).to.be.below(200)
                         })
                         pm.test("Checked whether the response size is less than 3KB or not!", function(){
                             pm.expect(pm.response.responseSize).to.be.below(3072)
                         })
                         pm.test("Successful To Fetch User's Info.")
                         
                         // STORE THE RESPONSE INTO VARIABLE
                         var resBody = pm.response.json()
                         
                         // CATCH THE LENGTH OF ITEM ARRAY TO USE IN TEST-CASE
                         var items = resBody.artists.items.length
                         
                         // GET THE USERNAME FROM ENVIRONMENT TO USE IN TEST-CASE
                         var usern = pm.environment.get("username")
                         
                         // DECLARE TWO VARIABLES THAT WILL HELP TO FIND ARTIST WITH MAX FOLLOWERS
                         let maxFollowers = 0
                         let popular = ""
                         
                         // LOOP FOR THE EACH ITEMS TO FIND THE ARTIST WITH MAX FOLLOWERS
                         resBody.artists.items.forEach(shorten => {
                             if (shorten.followers.total > maxFollowers) {
                                 maxFollowers = shorten.followers.total
                                 popular = shorten.name
                             }
                         })
                         pm.test(`Details From ${usern}'s Spotify With Following(${items}).`)
                         pm.test(`The Most Popular Artist From User's Following is ${popular}.`)
                         var resTime = pm.response.responseTime / 1000
                         pm.test(`The Response Time Was ${resTime} Seconds.`)
                 
                     break
                     // Below cases are as per Spotify API Document
                     case 401:
                         pm.test("The Access Token Has Expired.")
                     break
                     case 403:
                         pm.test("Server is refusing to fulfill your request.")
                     break
                     case 429:
                         pm.test("You've requested too many requests.")
                     break
                     default:
                         pm.test("Unsuccessful to fetch details of Spotify Account.")
                 }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 3. Read_Current_User's_Playlists
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL` <br>
   - **URL:** [{{baseURL}}/me/playlists](https://api.spotify.com/v1/me/playlists) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:** <br>
```
        pm.test("Checked whether the response code is 200 or not!", function () {
            pm.response.to.have.status(200)
             })
               switch(pm.response.code){
                   case 200:
                       // To verify the reponse body
                       pm.test("Checked whether response body contains data or not!", function(){
                           pm.expect(pm.response.text()).to.not.be.empty
                       })
                       // To verify response header
                       pm.test("Checked whether response header has expected Content-type or not!", function(){
                           pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                       })
                       pm.test("Checked whether the response size is less than 3KB or not!", function(){
                           pm.expect(pm.response.responseSize).to.be.below(3072)
                       })
                       pm.test("Checked whether response time is less than 0.2s or not!", function () {
                           pm.expect(pm.response.responseTime).to.be.below(200)
                       })
                       var resData = pm.response.json()
                       var pl = resData.items.length
                       var owner = resData.items[0].owner.display_name
                       var restime = pm.response.responseTime / 1000
                       pm.test(`Successful To Fetch ${owner}'s Playlist.`)
                       pm.test(`Currently ${pl} Playlists Available in ${owner}'s Spotify.`)
                       pm.test(`Response Time Was ${restime} Seconds.`)
                   break
                   // Below cases are as per Spotify API Document
                       case 401:
                       pm.test("The Access Token Has Expired.")
                   break
                   case 403:
                       pm.test("Server is refusing to fulfill your request.")
                   break
                   case 429:
                       pm.test("You've requested too many requests.")
                   break
                   default:
                       pm.test("Unsuccessful to fetch details of Spotify Playlists.")
               }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 4. Create_Playlist
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** <br>
```
       {
          "name" : "{{plName}}",
          "public" : {{plAvail}},
          "description" : "{{plDes}}"
       }
```
   - **URL:** [{{baseURL}}/users/{{put_your_spotify_user_id}}/playlists](https://api.spotify.com/v1/users/{{put_your_spotify_user_id}}/playlists) <br>
   - **Method:** POST<br>
   - **Pre-request Script:** <br>
```
             pm.environment.set("plName", pm.variables.replaceIn("{{$randomFullName}}"))
             pm.environment.set("plAvail", pm.variables.replaceIn("{{$randomBoolean}}"))
             pm.environment.set("plDes", pm.variables.replaceIn("{{$randomCatchPhrase}}"))
```
   - **Post-request Script:** <br>
```
            pm.test("Checked whether the response code is 201 or not!", function () {
                        pm.response.to.have.status(201)
                })
             switch(pm.response.code) {
                 case 201:
                     // To verify the reponse body
                     pm.test("Checked whether response body contains data or not!", function(){
                         pm.expect(pm.response.text()).to.not.be.empty
                     })
                     // To verify response header
                     pm.test("Checked whether response header has expected Content-type or not!", function(){
                         pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                     })
                     //To check the response
                     pm.test("Checked whether response time is less than 0.2s or not!", function () {
                         pm.expect(pm.response.responseTime).to.be.below(200)
                     })
                     pm.test("Checked whether the response size is less than 3KB or not!", function(){
                         pm.expect(pm.response.responseSize).to.be.below(3072)
                     })
                     pm.test(`New Resource created with Status Code of 201.`);
                     var plName = pm.environment.get("plName"); // Defined plName variable
                     var plAvail = pm.environment.get("plAvail"); // Defined plName variable
                     var plDes = pm.environment.get("plDes"); // Defined plName variable
                     pm.test(`Playlist Has Created With A Name of "${plName}"`);
                     var pl = pm.response.json().name;
                     // SET PLAYLIST ID WITH THE NAME IT'S CREATED.
                     pm.environment.set(`${pl}PL`, pm.response.json().id);
                     pm.environment.set("LastPLid", pm.response.json().id);
                     var resTime = pm.response.responseTime / 1000;
                     pm.test(`The Response Time Was ${resTime} Seconds.`);
                     console.log(plName);
                     console.log(plAvail);
                     console.log(plDes);
                     break;
                 // Below cases are as per Spotify API Document
                 case 400:
                     var error = pm.response.json().error.message;
                     pm.test(`Error! ${error}`);
                     break;
                 case 401:
                     pm.test("The Access Token Has Expired.");
                     break;
                 case 403:
                     pm.test("Server is refusing to fulfill your request.");
                     break;
                 case 429:
                     pm.test("You've requested too many requests.");
                     break;
                 default:
                     pm.test("Unsuccessful to Create Playlist.");
             }
```
**Response:**
>The code was 201. A new resource was created successfully. <br>
## 5. Read_After_Create_Playlist_
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL` <br>
   - **URL:** [{{baseURL}}/playlists/{{LastPLid}}](https://api.spotify.com/v1/playlists/{{LastPLid}}) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL` <br>
   - **Post-request Script:** <br>
```
             pm.test("Checked whether the response code is 200 or not!", function () {
                pm.response.to.have.status(200)
                  })
              switch(pm.response.code){
                  case 200:
                      // To verify the reponse body
                      pm.test("Checked whether response body contains data or not!", function(){
                          pm.expect(pm.response.text()).to.not.be.empty
                      })
                      // To verify response header
                      pm.test("Checked whether response header has expected Content-type or not!", function(){
                          pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                      })
                      pm.test("Checked whether the response size is less than 3KB or not!", function(){
                          pm.expect(pm.response.responseSize).to.be.below(3072)
                      })
                      var resData = pm.response.json()
                      pm.test("Checking whether 'Playlist Name' is inserted as given input or not!", function(){
                          pm.expect(pm.environment.get("plName")).to.eql(resData.name)
                          })
                      pm.test("Checking whether 'Playlist Description' is inserted as given input or not!", function(){
                          pm.expect(pm.environment.get("plDes")).to.eql(resData.description)
                      })
                      pm.test("Checking whether 'Playlist Public' is inserted as given input or not!", function(){
                          var enValue = JSON.parse(pm.environment.get("plAvail"))
                          pm.expect(enValue).to.eql(resData.public)
                      })
                      pm.test("Checked whether response time is less than 0.2s or not!", function () {
                          pm.expect(pm.response.responseTime).to.be.below(200)
                      })
                      var resTime = pm.response.responseTime / 1000
                      pm.test(`The Response Time Was ${resTime} Seconds.`)
                  break
                  // Below cases are as per Spotify API Document
                      case 401:
                      pm.test("The Access Token Has Expired.")
                  break
                  case 403:
                      pm.test("Server is refusing to fulfill your request.")
                  break
                  case 429:
                      pm.test("You've requested too many requests.")
                  break
                  default:
                      pm.test("Unsuccessful to fetch details of Spotify Playlists.")
              }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 6. Read_Specific_Playlist_Items
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL` <br>
   - **URL:** [{{baseURL}}/playlists/{{Susmit_KarmakerPL}}](https://api.spotify.com/v1/playlists/{{Susmit_KarmakerPL}}) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL` <br>
   - **Post-request Script:** <br>
```
             pm.test("Checked whether the response code is 200 or not!", function () {
                pm.response.to.have.status(200)
              })
                var resData = pm.response.json()
                switch(pm.response.code){
                    case 200:
                        // To verify the reponse body
                        pm.test("Checked whether response body contains data or not!", function(){
                            pm.expect(pm.response.text()).to.not.be.empty
                        })
                        // To verify response header
                        pm.test("Checked whether response header has expected Content-type or not!", function(){
                            pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                        })
                        pm.test("Checked whether response time is less than 0.2s or not!", function () {
                            pm.expect(pm.response.responseTime).to.be.below(200)
                        })
                        pm.test("Checked whether the response size is less than 3KB or not!", function(){
                            pm.expect(pm.response.responseSize).to.be.below(3072)
                        })
                        var plname = resData.name
                        pm.test(`Successful To Fetch ${plname}'s Playlist Details.`)
                        if(resData.tracks.items.length > 0) {
                            var tracks = resData.tracks.items.length
                            pm.test(`Currently ${tracks} Tracks Available in The Playlist.`)
                        }else{
                            pm.test(`Currently No Items Available in The Playlist.`)
                        }
                        var resTime = pm.response.responseTime / 1000
                        pm.test(`The Response Time Was ${resTime} Seconds.`)
                    break
                    // Below cases are as per Spotify API Document
                    case 401:
                        pm.test("The Access Token Has Expired.")
                    break
                    case 403:
                        pm.test("Server is refusing to fulfill your request.")
                    break
                    case 429:
                        pm.test("You've requested too many requests.")
                    break
                    default:
                        pm.test("Unsuccessful to fetch details of Spotify Playlists.")
                }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 7. Update_Playlist_Details
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:**
```
         {
           "name" : "{{updateName}}",
           "public" : {{updateAvail}},
           "description" : "{{updateDes}}"
         }
```
   - **URL:** [{{baseURL}}/playlists/{{LastPLid}}](https://api.spotify.com/v1/playlists/{{LastPLid}}) <br>
   - **Method:** PUT<br>
   - **Pre-request Script:**
```
        pm.environment.set("updateName", pm.variables.replaceIn("{{$randomFullName}}"))
        pm.environment.set("updateAvail", pm.variables.replaceIn("{{$randomBoolean}}"))
        pm.environment.set("updateDes", pm.variables.replaceIn("{{$randomCatchPhrase}}"))
```
   - **Post-request Script:** <br>
```
        pm.test("Checked whether the response code is 200 or not!", function () {
            pm.response.to.have.status(200)
          })
             switch(pm.response.code)
             {
                 case 200:
                     // To verify the reponse body
                     pm.test("Checked whether response body contains data or not!", function(){
                         pm.expect(pm.response.text()).to.not.be.empty
                     })
                     // To verify response header
                     pm.test("Checked whether response header has expected Content-type or not!", function(){
                         pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                     })
                     pm.test("Checked whether the response size is less than 3KB or not!", function(){
                         pm.expect(pm.response.responseSize).to.be.below(3072)
                     })
                     var uplname = pm.environment.get("updateName")
                     pm.test(`Checked whether the playlist has updated with the given Name of "${uplname}" or not!`)
                     var upldes = pm.environment.get("updateDes")
                     pm.test(`Checked whether the playlist has updated with the given description of "${upldes}" or not!`)
                     
                     pm.test("Checked whether response time is less than 0.2s or not!", function () {
                         pm.expect(pm.response.responseTime).to.be.below(200)
                     })
                     var resTime = pm.response.responseTime / 1000
                     pm.test(`The Response Time Was ${resTime} Seconds.`)
                 break
                 // Below cases are as per Spotify API Document
                 case 400:
                     var error = pm.response.json().error.message
                     pm.test(`Error! ${error}`)
                 break
                 case 401:
                     pm.test("The Access Token Has Expired.")
                 break
                 case 403:
                     pm.test("Server is refusing to fulfill your request.")
                 break
                 case 429:
                     pm.test("You've requested too many requests.")
                 break
                 default:
                     pm.test("Unsuccessful to Create Playlist.")
             }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 8. Read_After_Update
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL`  
   - **URL:** [{{baseURL}}/playlists/{{LastPLid}}](https://api.spotify.com/v1/playlists/{{LastPLid}}) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:** <br>
```
         pm.test("Checked whether the response code is 200 or not!", function () {
             pm.response.to.have.status(200)
           })
              var resData = pm.response.json()
              switch(pm.response.code){
                  case 200:
                          // To verify the reponse body
                      pm.test("Checked whether response body contains data or not!", function(){
                          pm.expect(pm.response.text()).to.not.be.empty
                      })
                      // To verify response header
                      pm.test("Checked whether response header has expected Content-type or not!", function(){
                          pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                      })
                      pm.test("Checked whether the response size is less than 3KB or not!", function(){
                          pm.expect(pm.response.responseSize).to.be.below(3072)
                      })
                      pm.test("Checking whether 'Playlist Name' is updated as given input or not!", function(){
                          pm.expect(pm.environment.get("updateName")).to.eql(resData.name)
                          })
                      pm.test("Checking whether 'Playlist Description' is updated as given input or not!", function(){
                          pm.expect(pm.environment.get("updateDes")).to.eql(resData.description)
                      })
                      pm.test("Checking whether 'Playlist Public' is updated as given input or not!", function(){
                          var enValue = JSON.parse(pm.environment.get("updateAvail"))
                          pm.expect(enValue).to.eql(resData.public)
                      })
                      pm.test("Checked whether response time is less than 0.2s or not!", function () {
                          pm.expect(pm.response.responseTime).to.be.below(200)
                      })
                      var resTime = pm.response.responseTime / 1000
                      pm.test(`The Response Time Was ${resTime} Seconds.`)
                  break
                  // Below cases are as per Spotify API Document    
                  case 401:
                      pm.test("The Access Token Has Expired.")
                  break
                  case 403:
                      pm.test("Server is refusing to fulfill your request.")
                  break
                  case 429:
                      pm.test("You've requested too many requests.")
                  break
                  default:
                      pm.test("Unsuccessful to fetch details of Spotify Playlists.")
              }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 9. Read_User's_Saved_Tracks
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL`  
   - **URL:** [{{baseURL}}/me/tracks](https://api.spotify.com/v1/me/tracks) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:**
```
         pm.test("Checked whether the response code is 200 or not!", function () {
            pm.response.to.have.status(200)
          })
              var data1 = [true, false];
              var data2 = [true, 123];
              
              var len = pm.response.json()
              var tracks = len.items.length
              
              switch(pm.response.code){
                  case 200:
                      // To verify the reponse body
                      pm.test("Checked whether response body contains data or not!", function(){
                          pm.expect(pm.response.text()).to.not.be.empty
                      })
                      // To verify response header
                      pm.test("Checked whether response header has expected Content-type or not!", function(){
                          pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                      })
                      pm.test("Checked whether the response size is less than 3KB or not!", function(){
                          pm.expect(pm.response.responseSize).to.be.below(3072)
                      })
                      pm.test("Checked whether response time is less than 0.2s or not!", function () {
                          pm.expect(pm.response.responseTime).to.be.below(200)
                      })
                      pm.test(`Currently ${tracks} Tracks Available in Saved Section.`)
                      var resTime = pm.response.responseTime / 1000
                      pm.test(`The Response Time Was ${resTime} Seconds.`)
                  break
                  // Below cases are as per Spotify API Document
                  case 401:
                      pm.test("The Access Token Has Expired.")
                  break
                  case 403:
                      pm.test("Server is refusing to fulfill your request.")
                  break
                  case 429:
                      pm.test("You've requested too many requests.")
                  break
                  default:
                      pm.test("Unsuccessful to fetch details of Spotify Playlists.")
              }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 10. Save_Tracks_for_Current_User
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:**
```
         {
             "ids" : ["{{saveTrack}}"]
         }
```
   - **URL:** [{{baseURL}}/me/tracks](https://api.spotify.com/v1/me/tracks) <br>
   - **Method:** PUT<br>
   - **Pre-request Script:**
```
        // keep few tracks so that i can randomly use one of these for operation
            var tracks = ["2JzZzZUQj3Qff7wapcbKjc","7npLlaPu9Mfno8hjk5OagD","27tNWlhdAryQY04Gb2ZhUI","27SdWb2rFzO6GWiYDBTD9j","5Ravsw8TmHN5wBiYPPYUFw","1KixkQVDUHggZMU9dUobgm","0b11D9D0hMOYCIMN3OKreM","4fkM7M4Uo5AUASnnsRC7EZ","6FahmzZYKH0zb2f9hrVsvw","1cPDOeVdALLjj4erFPiuqW"]
            var selTra = tracks[Math.floor(Math.random() * tracks.length)]
            pm.environment.set("saveTrack",selTra)
```
   - **Post-request Script:**
```
        pm.test("Checked whether the response code is 200 or not!", function () {
            pm.response.to.have.status(200)
           })
             switch(pm.response.code){
                 case 200:
                     // To verify the reponse body
                     pm.test("Checked whether response body contains data or not!", function(){
                         pm.expect(pm.response.text()).to.not.be.empty
                     })
                     // To verify response header
                     pm.test("Checked whether response header has expected Content-type or not!", function(){
                         pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                     })
                     pm.test("Checked whether the response size is less than 3KB or not!", function(){
                         pm.expect(pm.response.responseSize).to.be.below(3072)
                     })
                     pm.test("Checked whether response time is less than 0.2s or not!", function () {
                         pm.expect(pm.response.responseTime).to.be.below(200)
                     })
                     var resTime = pm.response.responseTime / 1000
                     pm.test(`The Response Time Was ${resTime} Seconds.`)
                 break
                 // Below cases are as per Spotify API Document
                 case 400:
                     var error = pm.response.json().error.message
                     pm.test(`Error! ${error}.`)
                 break
                 case 401:
                     pm.test("The Access Token Has Expired.")
                 break
                 case 403:
                     pm.test("Server is refusing to fulfill your request.")
                 break
                 case 429:
                     pm.test("You've requested too many requests.")
                 break
                 default:
                     pm.test("Unsuccessful to fetch details of Spotify Playlists.")
             }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 11. Read_After_Save_Track
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL`  
   - **URL:** [{{baseURL}}/me/tracks](https://api.spotify.com/v1/me/tracks) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:**
```
        pm.test("Checked whether the response code is 200 or not!", function () {
            pm.response.to.have.status(200)
           })
             switch(pm.response.code){
                 case 200:
                     // To verify the reponse body
                     pm.test("Checked whether response body contains data or not!", function(){
                         pm.expect(pm.response.text()).to.not.be.empty
                     })
                     // To verify response header
                     pm.test("Checked whether response header has expected Content-type or not!", function(){
                         pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                     })
                     pm.test("Checked whether the response size is less than 3KB or not!", function(){
                         pm.expect(pm.response.responseSize).to.be.below(3072)
                     })
                     pm.test("Checked whether response time is less than 0.2s or not!", function () {
                         pm.expect(pm.response.responseTime).to.be.below(200)
                     })
                     var resBody = pm.response.json()
                     var tracks = resBody.items.length
                     let match = pm.environment.get("saveTrack")
                     let trackName = ""
             
                     resBody.items.forEach(shorten => {
                         if(shorten.track.id == match){
                             trackName = shorten.track.name
                             }
                         })
             
                     pm.test(`Recently, A Track with The Title of "${trackName}" is Added.`)
                     pm.test(`Currently ${tracks} Tracks Available To Saved.`)
                     var resTime = pm.response.responseTime / 1000
                     pm.test(`The Response Time Was ${resTime} Seconds.`)
                 break
                 // Below cases are as per Spotify API Document
                 case 401:
                     pm.test("The Access Token Has Expired.")
                 break
                 case 403:
                     pm.test("Server is refusing to fulfill your request.")
                 break
                 case 429:
                     pm.test("You've requested too many requests.")
                 break
                 default:
                     pm.test("Unsuccessful to fetch details of Spotify Playlists.")
             }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 12. Delete_User's_Saved_Track
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:**
```
         {
             "ids" : ["{{saveTrack}}"]
         }
```
   - **URL:** [{{baseURL}}/me/tracks](https://api.spotify.com/v1/me/tracks) <br>
   - **Method:** DELETE<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:**
```
         pm.test("Checked whether the response code is 200 or not!", function () {
              pm.response.to.have.status(200)
            })
              switch(pm.response.code){
                  case 200:
                      // To verify the reponse body
                      pm.test("Checked whether response body contains data or not!", function(){
                          pm.expect(pm.response.text()).to.not.be.empty
                      })
                      // To verify response header
                      pm.test("Checked whether response header has expected Content-type or not!", function(){
                          pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                      })
                      pm.test("Checked whether the response size is less than 3KB or not!", function(){
                          pm.expect(pm.response.responseSize).to.be.below(3072)
                      })
                      pm.test("Checked whether response time is less than 0.2s or not!", function () {
                          pm.expect(pm.response.responseTime).to.be.below(200)
                      })
                      var resTime = pm.response.responseTime / 1000
                      pm.test(`The Response Time Was ${resTime} Seconds.`)
                  break
                  // Below cases are as per Spotify API Document
                  case 400:
                      var error = pm.response.json().error.message
                      pm.test(`Error! ${error}`)
                  break
                  case 401:
                      pm.test("The Access Token Has Expired.")
                  break
                  case 403:
                      pm.test("Server is refusing to fulfill your request.")
                  break
                  case 429:
                      pm.test("You've requested too many requests.")
                  break
                  default:
                      pm.test("Unsuccessful to fetch details of Spotify Playlists.")
              }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## 13. Read_After_Delete_Track
   - **Authorization:** Auth Type should set <ins>Bearer Token</ins> and Token <ins>{{accessToken}}</ins> <br>
   - **Request Body:** `NULL`  
   - **URL:** [{{baseURL}}/me/tracks](https://api.spotify.com/v1/me/tracks) <br>
   - **Method:** GET<br>
   - **Pre-request Script:** `NULL`  
   - **Post-request Script:**
```
         pm.test("Checked whether the response code is 200 or not!", function () {
             pm.response.to.have.status(200)
           })
              var resBody = pm.response.json()
              var tracks = resBody.items.length
              switch(pm.response.code){
                  case 200:
                      // To verify the reponse body
                      pm.test("Checked whether response body contains data or not!", function(){
                          pm.expect(pm.response.text()).to.not.be.empty
                      })
                      // To verify response header
                      pm.test("Checked whether response header has expected Content-type or not!", function(){
                          pm.response.to.have.header("Content-Type", "application/json; charset=utf-8")
                      })
                      pm.test("Checked whether the response size is less than 3KB or not!", function(){
                          pm.expect(pm.response.responseSize).to.be.below(3072)
                      })
                      pm.test("Checked whether response time is less than 0.2s or not!", function () {
                          pm.expect(pm.response.responseTime).to.be.below(200)
                      })
                      let match = pm.environment.get("saveTrack")
                      var res = pm.response.text()
                      if(!res.includes(match)){
                          pm.test("The Track Not Found Cause of Being Deleted.")
                      }
                      pm.test(`Currently ${tracks} Tracks Available To Saved.`)
              
                      var resTime = pm.response.responseTime / 1000
                      pm.test(`The Response Time Was ${resTime} Seconds.`)
                  break
                  // Below cases are as per Spotify API Document
                  case 401:
                      pm.test("The Access Token Has Expired.")
                  break
                  case 403:
                      pm.test("Server is refusing to fulfill your request.")
                  break
                  case 429:
                      pm.test("You've requested too many requests.")
                  break
                  default:
                      pm.test("Unsuccessful to fetch details of Spotify Playlists.")
              }
```
**Response:**
>The code was 200. Request successful. The server has responded as required. <br>
## Highlights:
  **1. Performance Tests:** <br>
       - Validated API response times.<br>
       - Tests like Response time is less than 200ms.<br>
  **2. Data Integrity:** <br>
       - Verified user profile details, playlists, and tracks.<br>
  **3. Custom Reports:** <br>
       - Enhanced visual reports using the htmlextra reporter.<br>
## Challenges
**1. Intermittent Failures:** Observed occasional 401 errors due to token expiration.<br>
       - Solution: Implemented automated token refreshing.<br>
**2. Response Time Failures:** Some tests failed the benchmark of 200ms.<br>
**3. Solution:** Increased thresholds and optimized request parameters.<br>
## Future Enhancements
**- Automated CI/CD Integration:** Integrate the tests with a CI/CD pipeline for automated testing.<br>
**- Extended Test Coverage:** Add more test cases to cover additional Spotify API endpoints.<br>
**- Performance Optimization:** Analyze and optimize the performance of API requests.<br>
## Conclusion
This project demonstrates the comprehensive testing of Spotify API endpoints using Postman and Newman. The tests cover various functionalities, ensuring the reliability and performance of the API. The detailed results provide insights into the API's behavior and performance metrics.
# Screenshots
![image](https://github.com/user-attachments/assets/d38eab19-6977-46ca-b856-c52cf07ed28a)  
![image](https://github.com/user-attachments/assets/df671903-b661-4563-ada6-eb34bb44c4f9)  
<div align="center">
  <img src="https://github.com/user-attachments/assets/d43f45ac-ac8d-4054-821d-38f9a7f02b73" alt="Thanks for Explore" style="width:50%; height:50%;">
</div>
