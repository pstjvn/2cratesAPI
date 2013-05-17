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
The backend should create all walls and all corners for the project. Corners count should be wall count - 1. If only one 
wall is created, the corners array should be empty but present.
Corner array contains record type with top and bottom properties, each can contain only one item.

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
    "corners": [ // corners.length == walls.length - 1
      {
        "top": null, // if no corner item is existing, use null and check for null (some implementations omit serialization!!!)
        "bottom": {
          "id": 1
          "is_attached_to_wall": true,
          "description": "whatever",
          "categoty_id": 4, // down corner
          "width": 300,
          "width2": 400, // used on the secondary wall ( wall 2 because it sits on the first corner)
          "height": 600,
          "depth": 350,
          "front_picture": "path/to/picture",
          "angle_picture": "path/to/picture",
          "has_top_board": true,
          "model_id": "23",
          "is_spacer": false,
          "price": 3500, // number, AU * 100 to avoid floating point arythmetics.
          "required_handles": 2, 
        }
      }
    ],
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
              "required_handles": 0,
              "is_secondary": true
              // flag: if it is true, then this item is cloned corner item that was initially mapped onto
              // the previous wall. The secondary width will be used in calculations and the price of this
              // items should not be included in price sum. the item cannot be moved from the current
              // wall (i.e. handle it in movement) and cannot be edited (i.e. ItemView editing) nor deleted,
              // the flag means basically 'could not touch this item'. On the other hand editing the
              // original (i.e. deleting, updating) should update this record as well. Becasue of
              // this up to one corner item is allowed per row per wall. All corner items that are
              // not clones MUST be the last on the row, all corner items that are clones MUST be
              // the first on the corresponding row. This should be accounted for in movement.
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

3 - there is no such wall id

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

15 - the attributes are not 3, 6 or 9

16 - cannot update item id for given position

17 - item category id and sent category do not match

18 - this user already have 5 projects

19 - this project is already saved

20 - this user is not registered

### Item categories

1 - up

2 - down

3 - up_corner

4 - down_corner

5 - two_row

6 - two_row_corner

7 - other
