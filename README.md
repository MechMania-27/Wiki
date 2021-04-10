# Wiki
Wikis for the game mechanics and starterpacks. 

## *TODO:*

*1. Start work on the Starterpacks part and add a section for API description*

# Game Mechanics
## A. Overview and Objective  
You and your cousins (competitors) are competing for your late uncle Phineas Worcestershire's estate.  
His will says that the estate goes to whomever makes the best use of his land, or more practically, whomever makes the most money.  

Mech Mania 27 will consist of a round-robin style tournament where each pair of teams will play a single head to head round.  

Each round consists of a single growing season, during which you and your opponent will try to make as much money as possible by growing your own crops, stealing your opponent's crops, as well as making use of special items and upgrades.

## B. The Field
Each round is played over a number of turns on a rectangular field which is 30 tiles wide and 50 tiles tall. The top several rows of the field cannot be planted on, and feature "Green Grocer" tiles, on which bots can buy seeds and sell produce.

At the begining of each round each bot will begin in either the top right or top left section of the field. They will have until the "Fertility Band" (representing the ideal growing environment) moves from the top of the board to the bottom, to make as much money as possible.

## C. The Fertility Band
The "Fertility Band" consists of 5 horrizontal regions which span the entire width of the board: A central "Ideal" region, two "Good" regions above and below the center, and two "OK" regions even farther from the center. Each region has a height in tiles as well as a fertility multiplier that affects tha value of plants grown in it, which are described below:

(Each line represents one row of tiles)
- OK     (1.0x)
- OK     (1.0x)
- OK     (1.0x)
- Good   (1.2x)
- Ideal  (1.5x)
- Good   (1.2x)
- OK     (1.0x)
- OK     (1.0x)
- OK     (1.0x)

Any tile lower than the fertility band can still be planted on, but has a fertility multiplier of 0. (The effects of this multiplier are described in "Growing Plants"). Any tile above the fertility band is infertile and cannot be planted on. In addition, any plants growing on a tile which becomes infertile (due to the fertility band moving down) will become worthless.

When each round begins, the fertility band's "Ideal" region will be on the first plantable tile (just beneath the unplantable region containg the "Green Grocer" tiles). Every 3 turns the entire fertility band will move down by one tile. Once the entire fertility band moves off the bottom of the board, the game ends. (i.e. at the begining of the turn where every tile of the field is "infertile", the game ends and neither bot gets to take an action)

## D. Taking your Turn

Each turn, bots can move and then perform one action.

### 1. Movement:

Bots will move up to their *MAX_MOVEMENT* toward the specified (x,y) board position.

>There are no collisions (with terrain or opponents) so there is no path-finding and two or more bots can occupy the same tile.
>
>If a bot moves onto a "Green Grocer" tile, all harvested crops in their inventory are automatically sold.
>
>Failure to meet the specifications of the *Move* action (e.g. trying to move to a tile past the bounds of the board or a tile outside the movement range) will result in the bot remaining at its previous location.

### 2. Actions:

#### a. Plant:

The bot plants any number of seeds in any combination of types within *PLANT_RADIUS* of their location.

>Crops cannot be planted at a location that is within *PROTECTION_RADIUS* of another bot or another bot's scarecrow.
>
>Failure to meet the specifications of the *Plant* action (e.g. trying to plant outside the plant range, not having enough seeds, or planting on a tile that already has a crop growing on it) will result in no action being taken.

#### b. Harvest:

The bot harvests the specified ripe crops (no matter which bot planted them) within *HARVEST_RADIUS* of their location.

>Crops within *PROTECTION_RADIUS* of another bot or another bot's scarecrow cannot be harvested.
>
>Failure to meet the specifications of the *Harvest* action (e.g. trying to harvest from a tile outside the harvest range) will result in no action being taken.
>
>Any crops the bot tries to harvest which cannot be carried (due to a full inventory) will be left alone.

#### c. Buy:

The bot buys any number of seeds in any combination of types from the Green Grocer

>The buy action cannot be taken unless the bot is on a "Green Grocer" tile.
>
>Failure to meet the specifications of the *Buy* action (e.g. trying to buy more than can be paid for, trying to buy more than can fit in the bot's inventory, or trying to buy a negative quantity of something) will result in no action being taken.
>
>If a bot only has enough money/inventory for part of a purchase, each (item, quantity) pair will be handled in order untill one fails.

#### d. Use Item:

The bot uses its item at its current location

>Failure to meet the specifications of the *Use Item* action (See Section F. *Items and Upgrades* for each item's required syntax) will result in no action being taken.

## E. Growing Plants

In order to grow crops you'll need seeds which can be purchased from the green grocer.

Each crop has a **growth_time** and **current value** paramater, and will grow over the course of *growh_time* turns. Its *current_value* begins at 0 and increases at the end of each turn based on the following formula:

>*current value* += *value_per_turn* * \[(1 - *fertility_sensitivity*) + (*tile_fertility* * *fertility_sensitivity*)]

Once *growth_time* turns have elapsed the crop will ripen, become harvestable, and no longer increase (or decrease) in value.

To explain the formula further:

**value_per_turn** is a property of the plant type, and represents the value the crop would gain each turn if it wasn't affected by the fertility band.

**tile_fertility** is a property of the tile the plant is on, and can change from one turn to the next. It represents how well-suited the tile is for plant growth.

**fertility_sensitivity** is a property of the plant type, and represents the proportion of the plant's *value_per_turn* that is multiplied by *tile_fertility*. 

To put the math into easier terms, a plant with a *fertility_sensitivity* of 0 is completely unaffected by the fertility band and will be worth *growth_time* * *value_per_turn* once it ripens, no matter what kind of tile it grows on. As *fertility_sensitivity* increases, the more affect the *tile_fertility* will have on the crop's final value.

### Plant List

#### **Crop** \[Fertility Sensitivity], \[Price], \[Growth Time], \[Value Per Turn]

**Potato** \[0%] \[$5] \[4 turns] \[1.375]

Basic plant, takes time to grow, can’t use fertility, but can make you an honest living

**Corn**, \[50%] \[$5] \[2 turns] \[2.5] 

Can use fertility and grows quickly. Can’t turn a profit without using the fertility band though

**Grapes**, \[50%] \[$15] \[10 turns] \[1.65] 

More expensive to plant, but sells for more. Can make minor use of fertility

**Jogan-Fruit** \[75%] \[$20] \[5 turns] \[4]

Also expensive, but makes great use of the fertility band

**Peanuts** \[0%] \[$5] \[30 turns] \[0.333] 

Troll crop used to take up space on the other farmer’s side

**Quadrotriticale** \[25%] \[$30] \[15 turns] \[2.6] 

Turns a solid profit with the fertility band, and is fairly low risk,  takes a long time to grow though

**Ducham-Fruit** \[100%] \[$100] \[5 turns] \[20] 

Can turn an amazing profit using the fertility band, but very cost prohibitive to acquire.

**Golden Corn** \[100%] \[$1000] \[3 turns] \[500] 

A once in a lifetime opportunity, this incredibly expensive seed, when planted in the most fertile regions can turn an equally incredible profit.



## F. Items and Upgrades

Each bot can take either 1 item of 1 upgrade with them for each match

Items can be activated to grant a powerful boost, but only one time per math

Upgrades increase the capabilities of the bot for the entire match but aren't as powerful as Items

### Item List

**Rain Totem**

Cause each crop in a 5x5 square centered on the farmer’s position to grow up to three times this turn instead of only once.

**Fertility Idol**

Doubles the fertility of all tiles within radius 2 for the next growth step. 

**Pesticide**

Decrease the current value of all crops within 1 radius by 20%.

**Scarecrow**

Protects tiles within radius 2 from harvest or planting by the opponent.(5x5)

**Delivery Drone**

Allows the farmer to buy and sell crops and seeds from anywhere on the farm for one turn.

**Coffee Thermos**

Triples the farmer’s MAX_MOVEMENT for the next turn

### Upgrade List


**Backpack**

CARRYING_CAPACITY increased.

**Electric Scythe**

HARVEST_RADIUS increased. (not PLANT_RADIUS though)

**Green Grocer Loyalty Card**

After spending $25 at the Green Grocer, buy seeds for 5% less.

**Moon Shoes**

MAX_MOVEMENT increased.

**Rabbit’s Foot**

Crops have a 5% chance of dropping twice when harvested.

**Seed-A-Pult**

PLANT_RADIUS increased

**Spyglass**

Increases PROTECTION_RADIUS

## G. Achivements

Achivements are fun goals to be completed accross the entire tournament. Completeing them doesn't grant any tournament standing, they're just for fun.

### Achivement List

**Not Worth the Dirt He Sows**

Finish a game with less money than you started.

**My Favorite Customer**

Spend $1,000 at the Green Grocer in one game.

**Dust Bowl**

Have 10 of your crops destroyed by the fertility zone passing over them.

**Seedy Business**

Harvest and sell a crop that another farmer planted.

**Omni-Agriculturalist**

Sell at least one crop of each type besides peanuts and golden corn in a single game.

**Grapes of Mild Displeasure**

Steal and sell 5 of your opponent’s grape crops.

**It Ain’t Much, but It’s Honest Work**

Win a game without ever harvesting a crop that your opponent planted.

**A Worthy Heir**

Win a game.

**Richer than Phineas Himself**

Have $2,500 or more in your account at the end of the game.

**Ornithophobia**

Place a scarecrow within the effect radius of the other farmer’s scarecrow.

**Stalks and Bonds**

Sell a golden corn

**Botanical Burglary**

Win a game without ever planting a crop

# Starter Packs
