# API

After going trou 'new project' -> select type (I, L, U), ->
select measurements - navigate to /designTool?kitchen_id=N

At this point backend should have created the kitchen project and provide us with the ID

web app should be able to pick up the ID and load it from server via API call.

* for all string values null is allowed
* for all numeric values 0 is allowed
* for all boolean values only true/false are allowed
* for all arrays empty array is allowed


### /loadKitchen?kitchen_project_id=N

retrieving kitchn project by ID
response:
```
{
  "status": "ok", // string, ok/fail
  "errorCode": 0, // number, 0 == ok, > 0 == error with name.
  // for example user A trying to access kitchen that she does not own
  "kitchen": {
    "id": "1", //string - should always be present for consistency checks
    "type": "I", //string - type can be I, L, or U
    "description": "", // string
    "pictures": ["path/to/picture"], // array of string, empty array is allowed
    "number_of_walls": 1, // number
    "handles_id": "1", // string
    "finish": "1", // string, the ID of the finish to apply
    "walls": [
      {
        "id": "1", // string (not sure if we need it yet)
        "width": 4000, // number, max width of the kitchen
        "is_attached_to_wall": true, // boolean
        "height": 2500, // number, for now it is a constant
        "items": {
          "top": [
            {
              "id": "1", // string, item id.
            },
            {
              // this is a spacer, the ID matches the spacer identificator, we
              // should have received the ID with the list of items!!! for this
              // to work saving project should take special care to iterate
              // items that are marked as spacers.
              "id": "231"
            }
          ],
          "bottom": [
            {
              "id": "17"
            }
          ]
        }
      }
    ]
  }
}
```
### /saveKitchen

same as above package except for new spacers. example: I am open to
sugestions here if you know way to make this easier to process on the
backend, for example requestig an ID each time the item is altered at the
client in order to unify the submission of data for storing a procjet on the
backend.

```
"top": [{
  "width": 300,
  "height": 550,
  "is_spacer": true
}]
```

Backend should be able to interpret this as 'new item' and perform insert in
items if needed (no width/height matching the item are found) and write only
the ID of the item.

### /getItems?kitchen_project_id=N

where N == kitchen project it, could be
non-existing project id and could be omitted.
response:

```
{
  "status": "ok",
  "errorCode": 0,
  "items": [
    {
      "id": 1
      "is_attached_to_wall": true,
      "description": "whatever",
      "categoty_id": "21", // up, down, angle, applience ..?
      "width": 300,
      "width2": 400, // ignored for items that do not have second back wall.
      "height": 600,
      "depts": 350,
      "front_picture": "path/to/picture",
      "3d_picture": "path/to/picture",
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
  "status": "ok",
  "errorCode": 0,
  "handles": [
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
  "status": "ok",
  "errorCode": 0,
  "finishes": [
    {
      "id": 1
      "description": "whatever",
      "picture": "path"
      "price": 2300
    }
  ]
}
```
