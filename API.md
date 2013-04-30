# API

After going trou 'new project' -> select type (I, L, U), ->
select measurements - navigate to /designTool?kitchen_id=N

At this point backend should have created the kitchen project and provide us with the ID

web app should be able to pick up the ID and load it from server via API call.

* for all string values null is allowed
* for all numeric values 0 is allowed
* for all boolean values only true/false are allowed
* for all arrays empty array is allowed

### Create a new project.

New project is created by linking to a page allowing configuration of basic kitchen parameters.
The following parameters are used:

* type=L // I, L, U
* walls=2 // number of walls (can be deducted from type)
* desc=whatever // Optional, description for the project
* attributes=w,h,a,... // array of wall settings, ( walls*3 number of items, 3 params for wach wall, width, height, attached).

Params are submitted as GET request with the data url encoded in the URI.

The backend should create the project and redirect to the design tool URL with kitchen_project_id filled in.

### /loadKitchen?kitchen_project_id=N

retrieving kitchn project by ID. The included items are inlined as full records to shorten load time
and improve working with spacers.

response:
```
{
  "status": 0, // number, 0 == ok, > 0 == error with name.
  // for example user A trying to access kitchen that she does not own
  // 1 = there is no project with this id
  "data": {
    "id": "1", //string - should always be present for consistency checks
    "type": "I", //string - type can be I, L, or U
    "description": "", // string
    "pictures": ["path/to/picture"], // array of string, empty array is allowed
    "number_of_walls": 1, // number
    "handles_id": "1", // string
    "finish_id": "1", // string, the ID of the finish to apply
    "walls": [
      {
        "id": "1", // string (not sure if we need it yet)
        "width": 4000, // number, max width of the kitchen
        "is_attached_to_wall": true, // boolean
        "height": 2500, // number, for now it is a constant
        "items": {
          "top": [
            {
              "id": 1
              "is_attached_to_wall": true,
              "description": "whatever",
              "categoty_id": 1, //number 1 = up, 2 = down, 3 = up_corner, 4 = down_corner, 5 = two_row, 6 = two_row_corner, 7 = other
              "width": 300,
              "width2": 400, // ignored for items that do not have second back wall.
              "height": 600,
              "depth": 350,
              "front_picture": "path/to/picture",
              "angle_picture": "path/to/picture",
              "has_top_board": true,
              "model_id": "23",
              "is_spacer": false,
              "price": 3500, // number, AU * 100 to avoid floating point arythmetics.
              "required_handles": 0, // not sure for this, how do we calculate price for handles?
            },
            {
              // this is a spacer, the ID matches the spacer identificator, we
              // should have received the ID with the list of items!!! for this
              // to work saving project should take special care to iterate
              // items that are marked as spacers.
              "id": 0, // Zero is allowed only for spacers and should be ignored
              "is_attached_to_wall": true, // defaults
              "description": "default", // defaults
              "categoty_id": 1, //defaults, should be set by client logic.
              "width": 300,
              "width2": 400, // ignored for items that do not have second back wall.
              "height": 600,
              "depth": 350, // irrelevant
              "front_picture": "path/to/picture", // irrelevant
              "angle_picture": "path/to/picture", // irrelevant
              "has_top_board": false, // irrelevant
              "model_id": "0", // ifrrelevant
              "is_spacer": true, // should always be true for spacers
              "price": 0, // irrelevant
              "required_handles": 0, // irrelevant
            }
          ],
          "bottom": []
        }
      }
    ]
  }
}
```
### /saveKitchen

Same as loadKitchen. Format is exactly the same, if a kitchen is loaded and withou
changes saved the payload should be exactly the same. POST method is used to send the payload.

```
{
    "id": "1", //string - should always be present for consistency checks
    "type": "I", //string - type can be I, L, or U
    "description": "", // string
    "pictures": ["path/to/picture"], // array of string, empty array is allowed
    "number_of_walls": 1, // number
    "handles_id": "1", // string
    "finish_id": "1", // string, the ID of the finish to apply
    "walls": [
      {
        "id": "1", // string (not sure if we need it yet)
        "width": 4000, // number, max width of the kitchen
        "is_attached_to_wall": true, // boolean
        "height": 2500, // number, for now it is a constant
        "items": {
          "top": [
            {
              "id": 1
              "is_attached_to_wall": true,
              "description": "whatever",
              "categoty_id": 1, //number 1 = up, 2 = down, 3 = up_corner, 4 = down_corner, 5 = two_row, 6 = two_row_corner, 7 = other
              "width": 300,
              "width2": 400, // ignored for items that do not have second back wall.
              "height": 600,
              "depth": 350,
              "front_picture": "path/to/picture",
              "angle_picture": "path/to/picture",
              "has_top_board": true,
              "model_id": "23",
              "is_spacer": false,
              "price": 3500, // number, AU * 100 to avoid floating point arythmetics.
              "required_handles": 0, // not sure for this, how do we calculate price for handles?
            },
            {
              // this is a spacer, the ID matches the spacer identificator, we
              // should have received the ID with the list of items!!! for this
              // to work saving project should take special care to iterate
              // items that are marked as spacers.
              "id": 0, // Zero is allowed only for spacers and should be ignored
              "is_attached_to_wall": true, // defaults
              "description": "default", // defaults
              "categoty_id": 1, //defaults, should be set by client logic.
              "width": 300,
              "width2": 400, // ignored for items that do not have second back wall.
              "height": 600,
              "depth": 350, // irrelevant
              "front_picture": "path/to/picture", // irrelevant
              "angle_picture": "path/to/picture", // irrelevant
              "has_top_board": false, // irrelevant
              "model_id": "0", // ifrrelevant
              "is_spacer": true, // should always be true for spacers
              "price": 0, // irrelevant
              "required_handles": 0, // irrelevant
            }
          ],
          "bottom": []
        }
      }
    ]
  }
```


### /getItems

response:

```
{
  "status": 0,
  "data": [
    {
      "id": 1
      "is_attached_to_wall": true,
      "description": "whatever",
      "categoty_id": 1, //number 1 = up, 2 = down, 3 = up_corner, 4 = down_corner, 5 = two_row, 6 = two_row_corner, 7 = other
      "width": 300,
      "width2": 400, // ignored for items that do not have second back wall.
      "height": 600,
      "depth": 350,
      "front_picture": "path/to/picture",
      "angle_picture": "path/to/picture",
      "has_top_board": true,
      "model_id": "23",
      "is_spacer": false,
      "price": 3500, // number, AU * 100 to avoid floating point arythmetics.
      "required_handles": 0, // not sure for this, how do we calculate price for handles?
    }
  ]
}
```

### /getHandles

```
{
  "status": 0,
  "data": [
    {
      "id": 1
      "description": "whatever",
      "picture": "path",
      "dimentions": "12x532",
      "price": 2200
    }
  ]
}
```

### /getFinishes

```
{
  "status": 0,
  "data": [
    {
      "id": 1
      "description": "whatever",
      "picture": "path",
      "color": "#dddddd",
      "price": 2300
    }
  ]
}
```

### /getModels

Should return the details for all models (mainly required for model names if required in UI).

```
{
  "status": 0,
  "data": [ {
    "id": "1",
    "name": "whatever"
  } ]
}
```


### Errors and error text

JSON errors

  {status: 0, message: "", data: {}}
  
  {status: 5, message: "some error"}
  
Status errors

1 - cannot create new item for given wall id

2 - cannot create new spacer

3 - cannot update wall(s) data

4 - cannot update kitchen project

5 - data not sent

6 - there is no project with this id

7 - can't load project pictures

8 - can't load wall(s) data

9 - can't load finishes

10 - can't load handles

11 - can't load items

12 - can't load item models

13 - this user have too many projects

14 - project is not created

Item categories

1 - up

2 - down

3 - up_corner

4 - down_corner

5 - two_row

6 - two_row_corner

7 - other
