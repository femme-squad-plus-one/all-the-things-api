# All The Things API

All The Things API is the backend process for the web application All The Things Client. It allows individuals to register as users of the API, upload and manage their own files, while sharing their uploads with others in the All The Things community.

# Links
* [All The Things API](https://guarded-cliffs-28165.herokuapp.com/)
* [All The Things Client](https://femme-squad-plus-one.github.io/all-the-things-client/)
* [All The Things Client Repository](https://github.com/femme-squad-plus-one/all-the-things-client)
* [All The Things ERD](https://docs.google.com/presentation/d/1V-vviYC3Lv05KdLfCZH7JuqWt9Un7KJBA2tOM_W7hAk/edit?usp=sharing)

# Technologies/npm Packages Used
* Node.js
* Mongo db

# Dependencies
Install with `npm install`
* moment
* aws-sdk
* multer
* express
* mongoose

# Installation
1. Fork and clone this repository.
2. Install dependencies with `npm install`.
3. Set a SECRET_KEY in the environment. For development and testing, set the SECRET_KEY from the root of your repository using `echo SECRET_KEY=$(/usr/local/opt/openssl/bin/openssl rand -base64 66 | tr -d '\n') >>.env`
4. Run the API server with `npm start`. If you want your code to be reloaded on change, you should `npm install -g nodemon` and use `nodemon` instead of `npm start`.

# Structure
* Dependencies are stored in `package.json`.
* Do not configure `grunt` packages directly in the
[`Gruntfile.js`](Gruntfile.js). Instead, store configurations in the
[`grunt`](grunt) directory. You won't need a top-level key, since that's
generated by the `Gruntfile.js` based on the filename of the configuration
object stored in the `grunt` directory.
* Developers should store JavaScript files in [`app/controllers`](app/controllers)
 and [`app/models`](app/models).
* Routes are stored in [`config/routes.js`](config/routes.js)
* Curl Scripts are stored in [`scripts/`](scripts/)


## API end-points
| Verb   | URI Pattern                    | Controller#Action         |
|--------|--------------------------------|---------------------------|
| POST   | `/sign-up`                     | `users#signup`            |
| POST   | `/sign-in`                     | `users#signin`            |
| DELETE | `/sign-out/:id`                | `users#signout`           |
| PATCH  | `/change-password/:id`         | `users#changepw`          |
| GET    | `/uploads`                     | `uploads#index`           |
| POST   | `/uploads`                     | `uploads#create`          |
| GET    | `/uploads/:id`                 | `uploads#show`            |
| PATCH  | `/uploads/:id`                 | `uploads#update`          |
| DELETE | `/uploads/:id`                 | `uploads#destroy`         |
| GET    | `/uploadowners`                | `users#usersWithDocs`     |
| GET    | `/folders/:id`                 | `uploads#folders`         |
| GET    | `/uploads/folder/:path/:owner` | `uploads#uploadsByFolder` |


All data returned from API actions is formatted as JSON.

---
## User actions

*Summary:*

<table>
<tr>
  <th colspan="3">Request</th>
  <th colspan="2">Response</th>
</tr>
<tr>
  <th>Verb</th>
  <th>URI</th>
  <th>body</th>
  <th>Status</th>
  <th>body</th>
</tr>
<tr>
<td>POST</td>
<td>`/sign-up`</td>
<td><strong>credentials</strong></td>
<td>201, Created</td>
<td><strong>user</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>POST</td>
<td>`/sign-in`</td>
<td><strong>credentials</strong></td>
<td>200 OK</td>
<td><strong>user w/token</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>401 Unauthorized</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>DELETE</td>
<td>`/sign-out/:id`</td>
<td>empty</td>
<td>201 Created</td>
<td>empty</td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>401 Unauthorized</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>PATCH</td>
<td>`/change-password/:id`</td>
<td><strong>passwords</strong></td>
<td>204 No Content</td>
<td><strong>user w/token</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><em>empty</em></td>
</tr>
</table>


#### POST /sign-up

Request:

```sh
curl --include --request POST http://localhost:4741/sign-up \
  --header "Content-Type: application/json" \
  --data '{
    "credentials": {
      "email": "an@example.email",
      "password": "an example password",
      "password_confirmation": "an example password"
    }
  }'
```

```sh
scripts/sign-up.sh
```

Response:

```md
HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8

{
  "user": {
    "id": 1,
    "email": "an@example.email"
  }
}
```

#### POST /sign-in

Request:

```sh
curl --include --request POST http://localhost:4741/sign-in \
  --header "Content-Type: application/json" \
  --data '{
    "credentials": {
      "email": "an@example.email",
      "password": "an example password"
    }
  }'
```

```sh
scripts/sign-in.sh
```

Response:

```md
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "user": {
    "id": 1,
    "email": "an@example.email",
    "token": "33ad6372f795694b333ec5f329ebeaaa"
  }
}
```

#### PATCH /change-password/:id

Request:

```sh
curl --include --request PATCH http://localhost:4741/change-password/$ID \
  --header "Authorization: Token token=$TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "passwords": {
      "old": "an example password",
      "new": "super sekrit"
    }
  }'
```

```sh
ID=1 TOKEN=33ad6372f795694b333ec5f329ebeaaa scripts/change-password.sh
```

Response:

```md
HTTP/1.1 204 No Content
```

#### DELETE /sign-out/:id

Request:

```sh
curl --include --request DELETE http://localhost:4741/sign-out/$ID \
  --header "Authorization: Token token=$TOKEN"
```

```sh
ID=1 TOKEN=33ad6372f795694b333ec5f329ebeaaa scripts/sign-out.sh
```

Response:

```md
HTTP/1.1 204 No Content
```

### Users

| Verb | URI Pattern | Controller#Action |
|------|-------------|-------------------|
| GET  | `/users`    | `users#index`     |
| GET  | `/users/1`  | `users#show`      |

#### GET /users

Request:

```sh
curl --include --request GET http://localhost:4741/users \
  --header "Authorization: Token token=$TOKEN"
```

```sh
TOKEN=33ad6372f795694b333ec5f329ebeaaa scripts/users.sh
```

Response:

```md
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "users": [
    {
      "id": 2,
      "email": "another@example.email"
    },
    {
      "id": 1,
      "email": "an@example.email"
    }
  ]
}
```

#### GET /users/:id

Request:

```sh
curl --include --request GET http://localhost:4741/users/$ID \
  --header "Authorization: Token token=$TOKEN"
```

```sh
ID=2 TOKEN=33ad6372f795694b333ec5f329ebeaaa scripts/user.sh
```

Response:

```md
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "user": {
    "id": 2,
    "email": "another@example.email"
  }
}
```



## Upload Actions

An upload is associated with a user. In the All The Things community, a user has the ability to read and download any content, even those belonging to other users. A user may only make updates to the uploads that s/he owns.

All upload action requests must include a valid HTTP header `Authorization: Token token=<token>` or they will be rejected with a status of 401 Unauthorized.

All of the upload actions, except for `placeholder`, follow the RESTful style.

## Description of Actions With Respect to Client Actions
The following maps the user actions/experience with the API actions:

* User signs up (#signon).
* After sign up, the user signs in (#signin).
* After successful sign in, the application displays the list of owner folders (#usersWithDocs).
* When the user selects on an owner's folder, the application displays the selected owner's list of sub folders (#folders) which are by upload date.
* When the user selects a sub folder, the application displays the selected owner's files that were uploaded on that date (#uploadsByFolder).
* Users are allowed to update the `title` of their own uploads (#update).
* Users are allowed to delete their own uploads (#destroy).

Note: `editable: true` is set for uploads whose `_owner` is the same as the user's `_id`. This is how the application determines when to show update and delete buttons.



*Summary:*

<table>
<tr>
  <th colspan="3">Request</th>
  <th colspan="2">Response</th>
</tr>
<tr>
  <th>Verb</th>
  <th>URI</th>
  <th>body</th>
  <th>Status</th>
  <th>body</th>
</tr>
<tr>
<td>GET</td>
<td>`/uploads`</td>
<td>n/a</td>
<td>200, OK</td>
<td><strong>uploads</strong></td>
</tr>
<tr>
  <td colspan="3">
  Any user is authorized to do a GET for a all uploads.
  </td>
  <td>401 Unauthorized</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>POST</td>
<td>`/uploads`</td>
<td>images</td>
<td>204, No Content</td>
<td><strong>n/a</strong></td>
</tr>
<tr>
  <td colspan="3">
  </td>
  <td>401 Unauthorized</td>
  <td><em>empty</em></td>
</tr>
<tr>
  <td colspan="3">
  </td>
  <td>400 Bad Request</td>
  <td><strong>errors</strong></td>
</tr>
<tr>
<td>GET</td>
<td>`/uploads/:id`</td>
<td>n/a</td>
<td>200, OK</td>
<td><strong>upload</strong</td>
</tr>
<tr>
  <td colspan="3">
  Any user is authorized to do a GET for a single upload.
  </td>
  <td></td>
  <td></td>
</tr>
<tr>
  <td colspan="3">
  </td>
  <td>401 Unauthorized</td>
  <td><em>empty</em></td>
</tr>
<tr>
  <td colspan="3">
  </td>
  <td>404 Not Found</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>PATCH</td>
<td>`/uploads/:id`</td>
<td><em>upload</em></td>
<td>204, No Content</td>
<td><strong>empty</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><strong>errors</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>GET</td>
<td>`/uploadowners`</td>
<td><strong>n/a</strong></td>
<td>200, OK</td>
<td><strong>users</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><strong>errors</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>404 Not Found</td>
  <td><em>empty</em></td>
</tr>
<tr>
<td>GET</td>
<td>`/uploads/folder/:path/:owner`</td>
<td><strong>n/a</strong></td>
<td>200, OK</td>
<td><strong>user</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><strong>errors</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>404 Not Found</td>
  <td><em>empty</em></td>
</tr>
<td>GET</td>
<td>`/folders/:id`</td>
<td><strong>n/a</strong></td>
<td>200, OK</td>
<td><strong>uploads</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>400 Bad Request</td>
  <td><strong>errors</strong></td>
</tr>
<tr>
  <td colspan="3"></td>
  <td>404 Not Found</td>
  <td><em>empty</em></td>
</tr>
</table>

### index

The `index` action is a *GET* that retrieves all the uploads.


script file with curl request:
`script/uploads/get-all-uploads.sh`


curl request:
```curl script
API="http://localhost:4741"
URL_PATH="/uploads"
TOKEN="<put in token value>"

curl "${API}${URL_PATH}" \
  --include \
  --request GET \
  --header "Authorization: Token token=$TOKEN"
```
The JSON response body will contain an array of uploads, e.g.:

```json
{
	"uploads": [
		{
			"_id": "591cc467536d1569b139bf1a",
			"updatedAt": "2017-05-18T14:04:02.174Z",
			"createdAt": "2017-05-17T21:45:11.114Z",
			"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-17/17e7f8f0d2b438d899a75bbab492563a.jpg",
			"title": "readme update",
			"path": "05-17-2017",
			"_owner": "591c59777727f733df13226d",
			"__v": 0,
			"tags": [],
			"length": 13,
			"id": "591cc467536d1569b139bf1a",
			"editable": true
		},
		{
			"_id": "591cc47d536d1569b139bf1b",
			"updatedAt": "2017-05-17T21:54:56.402Z",
			"createdAt": "2017-05-17T21:45:33.314Z",
			"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-17/09f3ca217a85d06b9ee14dceb9ccd17c.jpg",
			"title": "updated file",
			"path": "05-17-2017",
			"_owner": "591c59777727f733df13226d",
			"__v": 0,
			"tags": [],
			"length": 12,
			"id": "591cc47d536d1569b139bf1b",
			"editable": true
		},
		{
			"_id": "591d7e665ad4d7b4784f3d04",
			"updatedAt": "2017-05-18T10:58:46.076Z",
			"createdAt": "2017-05-18T10:58:46.076Z",
			"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-18/c81d3547b7727fb67625c47097b87d23.jpg",
			"title": "b's first file",
			"path": "05-18-2017",
			"_owner": "591c877eabc5913dc26bbfab",
			"__v": 0,
			"tags": [],
			"length": 14,
			"id": "591d7e665ad4d7b4784f3d04",
			"editable": false
		},
		{
			"_id": "591da719a88daecd40d9935e",
			"updatedAt": "2017-05-18T13:52:25.776Z",
			"createdAt": "2017-05-18T13:52:25.776Z",
			"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-18/4f3f37758d730f4dcb22268fa71d3156.jpg",
			"title": "for readme",
			"path": "05-18-2017",
			"_owner": "591c59777727f733df13226d",
			"__v": 0,
			"tags": [],
			"length": 10,
			"id": "591da719a88daecd40d9935e",
			"editable": true
		}
	]
}
```

If there are no uploads at all, the response body will contain
 an empty uploads array, e.g.:

```json
{
  "uploads": [
  ]
}
```


### create

The `create` action expects a *POST* of `image` that is created using the
[`FormData` interface](https://developer.mozilla.org/en-US/docs/Web/API/FormData), e.g. :

```html
<form id="add-item"  enctype="multipart/form-data">
  <fieldset>
    <legend>Upload a File</legend>
    <label>
      Title
      <input type="text" name="image[title]" placeholder='140 character limit' maxlength=140 required >
    </label>
    <label>
      Image
      <input type="file" name="image[file]" required>
    </label>
    <input type="submit" name="submit" value="Upload">
  </fieldset>
    </form>
```


If the request is successful, the response will have an HTTP Status of 201 Created, and the body will contain JSON of the created upload, e.g.:

```json
{
	"upload": {
		"__v": 0,
		"updatedAt": "2017-05-18T19:20:32.359Z",
		"createdAt": "2017-05-18T19:20:32.359Z",
		"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-18/0fcfcb08a12f4ded7586d76231151d94.txt",
		"title": "test",
		"path": "05-18-2017",
		"_owner": "591c59777727f733df13226d",
		"_id": "591df40084c548e52ec28ae8",
		"tags": [],
		"length": 4,
		"id": "591df40084c548e52ec28ae8",
		"editable": false
	}
}
```

If the request is unsuccessful, the response will have an HTTP Status of 400 Bad Request, and the response body will be JSON describing the errors.


 ### show

 The `show` action is a *GET* specifying the `id` of the upload to retrieve, e.g.:

script file with curl request:
`scripts\uploads\get-one-upload.sh`


curl script:
 ```curl
 API="http://localhost:4741"
 URL_PATH="/uploads"
 ID="<document id>"
 TOKEN="<put in token value>"

 curl "${API}${URL_PATH}/${ID}" \
   --include \
   --request GET \
   --header "Authorization: Token token=$TOKEN"
 ```
 If the request is successful the status will be 200, OK, and the response body
  will contain JSON for the upload requested, e.g.:

 ```json
 {
	"upload": {
		"_id": "591da719a88daecd40d9935e",
		"updatedAt": "2017-05-18T13:52:25.776Z",
		"createdAt": "2017-05-18T13:52:25.776Z",
		"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-18/4f3f37758d730f4dcb22268fa71d3156.jpg",
		"title": "for readme",
		"path": "05-18-2017",
		"_owner": "591c59777727f733df13226d",
		"__v": 0,
		"tags": [],
		"length": 10,
		"id": "591da719a88daecd40d9935e",
		"editable": false
	}
}
 ```

 ### update

 This `update` action expects a *PATCH* with changes to an upload's `title`,
  e.g.:

 ```html
 <form id="update-item" enctype="multipart/form-data">
  <fieldset>
    <legend>Update a File</legend>
    <label>
        Title
        <input type="text" name="upload[title]" id="update-upload-title"  maxlength=140 required>
    </label>
    <input type="submit" name="submit" value="Update" id="submit-update-form-button">
  </fieldset>
  </form>
 ```

script file with curl request:
`scripts/uploads/update-upload.sh`

curl request:
```curl
API="http://localhost:4741"
URL_PATH="/uploads"
ID="<document id>"
TOKEN="<put in token value>"

curl "${API}${URL_PATH}/${ID}" \
  --include \
  --request PATCH \
  --header "Content-Type: application/json" \
  --header "Authorization: Token token=${TOKEN}" \
  --data '{
    "upload": {
      "title": "'"${TITLE}"'"
    }
  }'
```
```json
 {
   "upload": {
     "title": "change title"
   }
 }
 ```

 If the request is successful, the response body will contain JSON containing an array of document owners and their respective id.

```json
 {
    "users": [
    	{
    		"email": "a",
    		"id": "591c59777727f733df13226d"
    	},
    	{
    		"email": "b",
    		"id": "591c877eabc5913dc26bbfab"
    	}
    ]
 }
 ```

 If the request is unsuccessful, the response will have an HTTP Status of 400 Bad Request, and the response body will be JSON describing the errors.


 ### destroy

 This `destroy` action expects a *DELETE* specifying the `id` of the upload to delete.

 script file with curl request:
 `scripts/uploads/delete-upload.sh`

 curl request:
 ```curl
 API="http://localhost:4741"
 URL_PATH="/uploads"
 ID="<document id>"
 TOKEN="<put in token value>"

 curl "${API}${URL_PATH}/${ID}" \
   --include \
   --request DELETE \
   --header "Authorization: Token token=${TOKEN}"

 ```

If the request is successful the response will have an HTTP status of 204 No
  Content.

If the request is unsuccessful the reponse will have an HTTP status of 400 Bad
   Request.


### usersWithDocs

The `usersWithDocs` action is a *GET* that retrieves all the users that have
uploads.

script file with curl request:
`scripts/users/allusers.sh`

curl request:
```curl
API="http://localhost:4741"
URL_PATH="/allusers"
TOKEN="<put in token value>"
curl "${API}${URL_PATH}" \
  --include \
  --request GET \
  --header "Authorization: Token token=$TOKEN"
```
If the request is successful, the response body will contain JSON containing an array of folder paths for the owner, e.g.:

```json
{
	"users": [
		{
			"email": "a",
			"id": "591c59777727f733df13226d"
		},
		{
			"email": "b",
			"id": "591c877eabc5913dc26bbfab"
		},
		{
			"email": "c",
			"id": "591e312527ac5de8be53ce58"
		}
	]
}
```

If the request is unsuccessful, the response will have an HTTP Status of 400 Bad
 Request, and the response body will be JSON describing the errors.


### uploadsByFolder
The `uploadsByFolder` action is a *GET* that retrieves all of an owner's folders names.


script file with curl request:
`scripts/uploads/get-folders.sh`

curl request:
```curl
API="http://localhost:4741"
URL_PATH="/folders"
TOKEN="<put in token value"
ID="<owner id>"
curl "${API}${URL_PATH}/${ID}" \
  --include \
  --request GET \
  --header "Authorization: Token token=$TOKEN"
```

If the request is successful, the response body will contain JSON containing an array of folder paths for the owner, e.g.:

```json
{
	"user": {
		"email": "a",
		"id": "591c59777727f733df13226d"
	},
	"folders": [
		"05-17-2017",
		"05-18-2017"
	]
}
```

If the request is unsuccessful, the response will have an HTTP Status of 400 Bad
 Request, and the response body will be JSON describing the errors.


###uploadsByFolder
The `uploadsByFolder` action is a *GET* that retrieves all an owner's documents for the specified `path`.

script file with curl request:
`scripts/uploads/get-uploads-folder.sh`

curl request:
```curl
API="http://localhost:4741"
URL_PATH="/uploads/folder"
TOKEN="<put in token value>"
FOLDER="05-16-2017"
ID="<owner id>"
curl "${API}${URL_PATH}/${FOLDER}/${ID}" \
  --include \
  --request GET \
  --header "Authorization: Token token=$TOKEN"
```
If the request is successful, the response body will contain JSON containing an array of documents for that owner's specified path, e.g.:

```json
{
	"uploads": [
		{
			"_id": "591cc467536d1569b139bf1a",
			"updatedAt": "2017-05-18T14:04:02.174Z",
			"createdAt": "2017-05-17T21:45:11.114Z",
			"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-17/17e7f8f0d2b438d899a75bbab492563a.jpg",
			"title": "readme update",
			"path": "05-17-2017",
			"_owner": "591c59777727f733df13226d",
			"__v": 0,
			"tags": [],
			"length": 13,
			"id": "591cc467536d1569b139bf1a",
			"editable": true
		},
		{
			"_id": "591cc47d536d1569b139bf1b",
			"updatedAt": "2017-05-17T21:54:56.402Z",
			"createdAt": "2017-05-17T21:45:33.314Z",
			"url": "https://angmas-bucket.s3.amazonaws.com/2017-05-17/09f3ca217a85d06b9ee14dceb9ccd17c.jpg",
			"title": "updated file",
			"path": "05-17-2017",
			"_owner": "591c59777727f733df13226d",
			"__v": 0,
			"tags": [],
			"length": 12,
			"id": "591cc47d536d1569b139bf1b",
			"editable": true
		}
	]
}
```
If the request is unsuccessful, the response will have an HTTP Status of 400 Bad Request, and the response body will be JSON describing the errors.
