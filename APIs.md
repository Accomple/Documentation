# Accomple Client APIs
Documentation for using accomple_database for Native App Development
>- [Website](http://accomple.sockets.live)
>- [Admin Login](http://api.accomple.sockets.live/admin/)
## 1. Common End Points (users/owners)
### Registration
- Request
>POST: http://api.accomple.sockets.live/accounts/register/

```json
{
	"username":  "johndoe@gmail.com",
	"password": "TestPassword"
	"first_name":  "John",
	"last_name":  "Doe",
	"phone_number":  "9999999999",
	"is_owner":  [true|false],
	"date_of_birth":  "2010-10-10",
	"profile_pic":  /*[image/'*'|null]*/,
}
```
- Success Response [Status: 201]
returns the request data along with `authentication token` in `token` field which is used to access private views
```json
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
error response indicates insufficient, invalid request data  or repeated username in database
```json
{
	"detail":  "serialization error"
}
```
<hr></hr>

### Login
- Request
>POST: http://api.accomple.sockets.live/accounts/login/
```json
{
	"username":  "johndoe@gmail.com",
	"password":  "TestPassword"
}
```
- Success Response [Status: 200]
returns account type details along with `authentication token`
```json
{
	"token":  "b832aee39de3780257ec9070466bb012ed8b75b8",
	"is_owner":  false,
	"is_superuser":  false,
	"is_verified":  false,
	"name":  "John Doe"
}
```
- Error Response [Status: 401]
```json
{
	"detail":  "Incorrect username or password"
}
```
<hr></hr>

### Verification
- **Request Header**
`Authorization: Token b832aee39de3780257ec9070466bb012ed8b75b8 `
```json
// raises 401 error when header is missing
{
	"detail":  "Authentication credentials were not provided."
}
```
- Request (get details)
>GET: http://api.accomple.sockets.live/accounts/verification_code/

- Response
returns email and verification status
```json
{
	"email":  "johndoe@gmail.com",
	"is_verified":  false
}
```
- Request (resend otp)
>POST:  http://api.accomple.sockets.live/accounts/verification_code/
```json
{
	"resend":  true
}
```

- Success Response [Status: 201]
resends otp key to provided email, phone number and returns `{}` as response

- Error Response [Status: 423]
raised when verified users sends this request
```json
{
	"detail":  "verified account"
}
```

- Request (submit otp)
>POST:  http://api.accomple.sockets.live/accounts/verification_code/
```json
{
	"otp":  "<key>"
}
```

- Success Response [Status: 202]
when otp is accepted
```json
{
	"is_verified":  true,
	"detail":  "OTP accepted, verification success"
}
```
- Error Response [Status: 423]
raised when verified users sends this request
```json
{
	"detail":  "verified account"
}
```
- Error Response [Status: 422]
raised when `key` send in `otp` field is invalid
```json
{
	"detail":  "Invalid OTP"
}
```
- Error Response [Status: 410]
raised when `key` send in `otp` field is expired (600s)
```json
{
	"detail":  "OTP expired"
}
```
<hr></hr>


## 2. User Specific End Points
### Active Cities
- Request
>GET: http://api.accomple.sockets.live/active_cities/names/
>
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
- POST: http://api.accomple.sockets.live/accounts/bookmark/add/building_id=<_id_>/
```json
{}
```
- Sucess Response [Status: 201]
```json
{
	"id":  <bookmark_id>,
	"user":  <user_id>,
	"building":  <building_id>
}
```
- Error Response [Status: 404]
raised when user/building is not found
```json
{
	"detail":  "Not found."
}
```

###  List Bookmarks 
> Must contain User auth_token in headers

- Request
- GET: http://api.accomple.sockets.live/accounts/bookmarks/get/user=me/
```json
{}
```
- Sucess Response [Status: 200]
list of apartments bookmarked by this user, same structure as in **Browse Accommodations**

- Error Response [Status: 404]
provided token doesn't match any user

