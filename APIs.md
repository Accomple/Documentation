# Accomple Client APIs
Documentation for using accomple_database for Native App Development
>- [Website](http://accomple.sockets.live)
>- [Admin Login](http://api.accomple.sockets.live/admin/)
## 1. Common End Points (users/owners)
### Registration
- Request
>POST: http://api.accomple.sockets.live/accounts/register/

```js
{
	"username":  "johndoe@gmail.com",
	"password": "TestPassword"
	"first_name":  "John",
	"last_name":  "Doe",
	"phone_number":  "9999999999",
	"is_owner":  [true|false],
	"date_of_birth":  "2010-10-10",
	"profile_pic":  [image/*|null],
}
```
- Success Response [Status: 201]
>returns the request data along with `authentication token` in `token` field which is used to access private views
```js
{
	"username":  "johndoe@gmail.com",
	"first_name":  "John",
	"last_name":  "Doe",
	"is_verified":  false,
	"phone_number":  "999999999",
	"is_owner":  false,
	"date_of_birth":  "2010-10-10",
	"is_superuser":  false,
	"profile_pic":  "/media/profile_pics/0jLqZTV-pbANMkldhn_fADP3uhRQWAZOho2uy8gc.png",
	"token":  "b832aee39de3780257ec9070466bb012ed8b75b8"
}
```
- Error Response [Status: 400]
>error response indicates insufficient, invalid request data  or repeated username in database
```js
{
	"detail":  "serialization error"
}
```
<hr></hr>

### Login
- Request
>POST: http://api.accomple.sockets.live/accounts/login/
```js
{
	"username":  "johndoe@gmail.com",
	"password":  "TestPassword"
}
```
- Success Response [Status: 200]
>returns account type details along with `authentication token`
```js
{
	"token":  "b832aee39de3780257ec9070466bb012ed8b75b8",
	"is_owner":  false,
	"is_superuser":  false,
	"is_verified":  false,
	"name":  "John Doe"
}
```
- Error Response [Status: 401]
```js
{
	"detail":  "Incorrect username or password"
}
```
<hr></hr>

### Verification
- **Request Header**
`Authorization: Token b832aee39de3780257ec9070466bb012ed8b75b8 `
```js
// raises 401 error when header is missing
{
	"detail":  "Authentication credentials were not provided."
}
```
- Request (get details)
>GET: http://api.accomple.sockets.live/accounts/verification_code/

- Response
>returns email and verification status
```js
{
	"email":  "johndoe@gmail.com",
	"is_verified":  false
}
```
- Request (resend otp)
>POST:  http://api.accomple.sockets.live/accounts/verification_code/
```js
{
	"resend":  true
}
```

- Success Response [Status: 201]
>resends otp key to provided email, phone number and returns `{}` as response

- Error Response [Status: 423]
>raised when verified users sends this request
```js
{
	"detail":  "verified account"
}
```

- Request (submit otp)
>POST:  http://api.accomple.sockets.live/accounts/verification_code/
```js
{
	"otp":  <key>
}
```

- Success Response [Status: 202]
>when otp is accepted
```js
{
	"is_verified":  true,
	"detail":  "OTP accepted, verification success"
}
```
- Error Response [Status: 423]
>raised when verified users sends this request
```js
{
	"detail":  "verified account"
}
```
- Error Response [Status: 422]
>raised when `key` send in `otp` field is invalid
```js
{
	"detail":  "Invalid OTP"
}
```
- Error Response [Status: 410]
>raised when `key` send in `otp` field is expired (600s)
```js
{
	"detail":  "OTP expired"
}
```
<hr></hr>

### Update Profile
> Must contain User auth_token in headers
- Request
 > PUT: http://api.accomple.sockets.live/accounts/profile/update/user=me/
```js
 {
	"first_name":  "John",
	"last_name":  "Doe",
	"phone_number":  "9999999999",
	"is_owner":  [true|false],
	"date_of_birth":  "2010-10-10",
	"profile_pic":  [image/*|null],
}
```
- Success Response [Status: 200]
```js
{
	"username":  "johndoe@gmail.com",
	"first_name":  "John",
	"last_name":  "Doe",
	"password":  "pbkdf2_sha256$216000$J8SarSvbrvTT$tnIpgiKlRMZT7KdpmI6dZjXih/9ExTUrQQgKR7JpMiY=",
	"is_verified":  true,
	"phone_number":  "999999999",
	"is_owner":  false,
	"date_of_birth":  "2010-10-10",
	"is_superuser":  false,
	"profile_pic":  "/media/profile_pics/31XLDBAYgTx3TL9sbfH9mWIKkS45vfL-rlnz4MjX.png"
}
```

- Error Response [Status: 400]
>error response indicates insufficient, invalid request data  or repeated username in database
```js
{
	"detail":  "serialization error"
}
```
<hr></hr>

### Update Password
> Must contain User auth_token in headers
- Request
 > PUT: http://api.accomple.sockets.live/accounts/password/update/user=me/
```js
{
	"current_password": "TestPassword",
	"new_password": "NewTestPassword",
}
```
- Success Response [Status: 200]
```js
{
	"detail":  "success"
}
```

- Error Response [Status: 400]
>raised on `new_password.length() < 8`
```js
{
	"detail":  "invalid new password (length<8)"
}
```

- Error Response [Status: 401]
>raised on invalid Current Password in `PUT` request
```js
{
	"detail":  "invalid current password"
}
```
<hr></hr>

### Delete Account
> Must contain User auth_token in headers
- Request
 > DELETE: http://api.accomple.sockets.live/accountsdelete/user=me/
```js
{
	"password": "TestPassword"
}
```
- Success Response [Status: 200]
```js
{
	"detail":  "success"
}
```
- Error Response [Status: 401]
>raised on invalid Password in `DELETE` request
```js
{
	"detail":  "invalid password"
}
```
<hr></hr>


## 2. User Specific End Points
### Active Cities
- Request
>GET: http://api.accomple.sockets.live/active_cities/names/

<hr></hr>

### Browse Accommodations
- Request
>GET: http://api.accomple.sockets.live/accommodations/
- **Filters**
>- city=<_city_name_> 
>- rent_lte=<_max_rent_>
>- rent_gte=<_min_rent_>
>- occupancy=<_no_of_beds_>
>- search=<_keyword_>
>- gender_label=<_M|F|U_>
>- zip_code=<_pin_code_>
>- near=<_latitude_>,<_longitude_>

above list shows all the filters implemented, multiple filters are applied by concatenating them with **`'&'`**. 
>For example:
>GET: http://api.accomple.sockets.live/accommodations/city=pune&rent_lte=15000/
>enlists all the accommodations in Pune with rent less than or equal to 15,000

<hr></hr>

### Apartment Detail
> Note: 
> Providing Token in header while sending this request
> ie: `Authorization: Token b832aee39de3780257ec9070466bb012ed8b75b8 `
> will provide  a boolean field `is_bookmarked` to detemine bookmarked status of the appartment by this user.
- Request
>GET: http://api.accomple.sockets.live/accommodations/building/get/id=<_id_>/

For Example:
>GET: http://api.accomple.sockets.live/accommodations/building/get/id=1/
fetchs the building detail for building_id = 1

<hr></hr>

###  Add Bookmark 
> Must contain User auth_token in headers

- Request
 POST: http://api.accomple.sockets.live/accounts/bookmark/add/building_id=<_id_>/
```js
{}
```
- Sucess Response [Status: 201]
```js
{
	"id":  <bookmark_id>,
	"user":  <user_id>,
	"building":  <building_id>
}
```
- Error Response [Status: 404]
>raised when user/building is not found
```js
{
	"detail":  "Not found."
}
```
<hr></hr>

###  List Bookmarks 
> Must contain User auth_token in headers

- Request
> GET: http://api.accomple.sockets.live/accounts/bookmarks/get/user=me/
```js
{}
```
- Sucess Response [Status: 200]
>list of apartments bookmarked by this user, same structure as in **Browse Accommodations**

- Error Response [Status: 404]
>provided token doesn't match any user
<hr></hr>

###  Delete Bookmarks
> Must contain User auth_token in headers

- Request
> DELETE: http://api.accomple.sockets.live/accounts/bookmarks/delete/id=<_id_>/
```js
{}
```
- Sucess Response [Status: 200]
```js
{
	"detail":  "success"
}
```

- Error Response [Status: 404]
>raised when bookmark is not found
```js
{
	"detail":  "Not Found"
}
```

- Error Response [Status: 409]
>raised when bookmark's user doesn't match with this user
```js
{
	"detail":  "invalid user"
}
```
<hr></hr>

###  Add Booking
> Must contain User auth_token in headers
> Check whether user is verified intended to be done by native app developer

- Request
> POST: http://api.accomple.sockets.live/accommodations/booking/add/id=<_room_id_>/
```js
{}
```
- Sucess Response [Status: 201]
```js
{
	"id":  <booking_id>,
	"booking_no":  <booking_no>,
	"room":  <room_id>,
	"user":  <user_id>,
	"booking_date":  <timestamp_string>
}
```
- Error Response [Status: 404]
>raised when user/room is not found

- Error Response [Status: 409]
>raised when no rooms are available or multiple bookings detected for this user
<hr></hr>

###  Active Booking
> Must contain User auth_token in headers
- Request
> GET: http://api.accomple.sockets.live/accounts/booking/get/user=me/

- Sucess Response [Status: 200]
```js
{
    "booking": {
        "id": 12,
        "booking_no": "382394591609",
        "room": 1,
        "user": 13,
        "booking_date": "2020-12-04T17:17:41.569270Z"
    },
    "room": {
        "id": 1,
        "building": 1,
        "description": "Room for two",
        "title": "Double Occupancy",
        "rent": 12000.0,
        "total": 25,
        "available": 22,
        "occupancy": 2,
        "is_verified": true
    },
    "building": {
        "id": 1,
        "owner": 1,
        "building_name": "Chester House",
        "street": "Renuka Nivas",
        "area": "Vishwashanti Colony",
        "city": "Pune",
        "state": "Maharashtra",
        "zip_code": "411043",
        "landmark": "Chester Plaza",
        "latitude": 18.598116,
        "longitude": 73.798964,
        "gender_label": "U",
        "display_pic": "/media/building_pics/w3k1SskAgCwxjH-vRjm00PvSASdzWdTduGaKQhig.jpg",
        "in_time": "23:00:00"
    },
    "owner": {
        "email": "exampleowner@gmail.com",
        "first_name": "owner",
        "last_name": "example",
        "phone_number": "8888888888"
    },
    "exists": true
}
```
if no booking exists then 
```js
{
	"exists":  false
}
```
<hr></hr>

###  Delete Booking
> Must contain User auth_token in headers

- Request
> DELETE: http://api.accomple.sockets.live/accommodations/booking/delete/id=<_booking_no_>/

- Sucess Response [Status: 200]
```js
{
	"detail":  "success",
}
```
- Error Response [Status: 404]
>raised when booking is not found

- Error Response [Status: 401]
>raised on invalid token or booking's user doesn't match with this user
<hr></hr>

## 3. Owner Specific End Points
> Pending
