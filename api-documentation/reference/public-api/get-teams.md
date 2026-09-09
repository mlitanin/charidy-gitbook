---
description: https://api.charidy.com/api/v1/campaign/<CID>/teams
---

# Get Teams

Parameters:

* id - campaign id
* q - search query
* group string -
* light 1/0 -
* parent\_only 1/0 -
* grandparent\_only 1/0 -
* skip\_parent 1/0 -
* skip\_grandparent 1/0 -
* converted\_currency 1/0 -
* extend\_media 1/0 -
* grandparent\_media 1/0 -
* extend\_levels 1/0 -
* grandparent\_levels 1/0 -
* extend\_children\_stats 1/0 -
* extend\_sefer\_torah 1/0 -
* extend\_parent\_team 1/0 -
* featured 1/0 -
* skip\_without\_goal 1/0 -
* skip\_without\_goal\_and\_donations 1/0 -
* skip\_without\_donations 1/0 -
* team\_id int - team id
* goal\_below int -
* goal\_above int -
* team\_ids int\[] - limited to 100
* team\_exclude\_ids int\[] -
* parent\_team\_id int -
* limit int - default 100
* offset int -
* sort string - value must be >2 symbols; default: "sort, name"; first symbol stands for asc/desc ("-" is descending, default ascending); allowed values: name, amount, donorscount, sort, goal, closest\_to\_goal, id, goal\_updated\_at, latest\_activity
* show\_hidden 1/0 -
* event\_page 1/0 -
* include\_grandchildren\_teams boolean (true / false) - works only when parent\_team\_id is set also



Metas affecting the API business logic:

* aggregate\_team\_stats - will aggregate the campaign team stats across other campaigns in the givingday
* include\_givingday\_teams - public GET endpoint for retrieving // campaign teams include teams from all sub-campaigns in the giving day



Response headers:

* x-search-teams - total search teams count, cached value (5s)
* x-total-teams - total teams count (without search query filter), cached value (5s)
* x-campaign-total-goal - campaign goal, taken cached value (15s)
* x-campaign-total-amount - campaign raised amount, taken cached value (15s)
* x-campaign-total-count - campaign total donation count, taken cached value (15s)
* x-campaign-total-goal-gd-aggr - GD campaign goal, taken cached value (15s)
* x-campaign-total-amount-gd-aggr - GD campaign raised amount, taken cached value (15s)
* x-campaign-total-count-gd-aggr - GD campaign total donation count, taken cached value (15s)
* x-children-total-goal - set only when parent\_team\_id param set



Response:

* Cache by the URL hash - 5s
