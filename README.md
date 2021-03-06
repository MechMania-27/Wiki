# Wiki
Wiki for the game's mechanics.

# Contents

- [Overview and Objectives](#a-overview-and-objectives)
- [The Field](#b-the-field)
- [The Fertility Band](#c-the-fertility-band)
- [Taking Your Turn](#d-taking-your-turn)
- [Growing Plants](#e-growing-plants)
- [Items and Upgrades](#f-items-and-upgrades)
- [Achievements](#g-achievements)
- [Registration](#registration)

# Game Mechanics
## A. Overview and Objectives
You and your cousins (competitors) are competing for your late uncle Phineas Worcestershire's estate.  
His will says that the estate goes to whomever makes the best use of his land, or more practically, whomever makes the most money.  

Mech Mania 27 will consist of a round-robin style tournament where each pair of teams will play a single head to head round.  

Each round consists of a single growing season, during which you and your opponent will try to make as much money as possible by growing your own crops, stealing your opponent's crops, as well as making use of special items and upgrades.

## B. The Field
Each round is played over a number of turns on a rectangular field which is 30 tiles wide and 50 tiles tall. The top several rows of the field cannot be planted on, and are "Grass". The central 5 tiles of the top-most row are "Green Grocer" tiles, on which bots can buy seeds and sell produce.

At the beginning of each round each bot will begin in either the top right or top left section of the field. They will have until the "Fertility Band" (representing the ideal growing environment) moves from the top of the board to the bottom, to make as much money as possible.

## C. The Fertility Band
The "Fertility Band" consists of 5 horizontal regions which span the entire width of the board: A central "Ideal" region, two "Good" regions above and below the center, and two "OK" regions even farther from the center. Each region has a height in tiles as well as a fertility multiplier that affects tha value of plants grown in it, which are described below:

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

When each round begins, the fertility band's **"OK"** region will be on the first plantable tile **on turn 13**. (just beneath the unplantable region containing the "Green Grocer" tiles). Every 3 turns the entire fertility band will move down by one tile. Once the entire fertility band moves off the bottom of the board, the game ends (this results in 180 turns). (i.e. at the beginning of the turn where every tile of the field is "infertile", the game ends and neither bot gets to take an action)

![Fertility Band GIF](images/FertilityBand.gif)

## D. Taking your Turn

Each turn, bots can move and then perform one action.

### 1. Movement:

Bots will move up to 10 tiles toward the specified (x,y) board position.

>There are no collisions (with terrain or opponents) so there is no path-finding and two or more bots can occupy the same tile.
>
>If a bot moves onto a "Green Grocer" tile, all harvested crops in their inventory are automatically sold.
>
>Failure to meet the specifications of the *Move* action (e.g. trying to move to a tile past the bounds of the board or a tile outside the movement range) will result in the bot remaining at its previous location.

### 2. Actions:

#### a. Plant:

The bot plants any number of seeds in any combination of types within 1 tile of their location.

>Crops cannot be planted at a location that is within 2 tiles of another bot or another bot's scarecrow.
>
>Failure to meet the specifications of the *Plant* action (e.g. trying to plant outside the plant range, not having enough seeds, or planting on a tile that already has a crop growing on it) will result in no action being taken.

#### b. Harvest:

The bot harvests the specified ripe crops (no matter which bot planted them) within 1 tile of their location.

>Crops within 2 tiles of another bot or another bot's scarecrow cannot be harvested.
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
>If a bot only has enough money/inventory for part of a purchase, each (item, quantity) pair will be handled in order until one fails.

#### d. Use Item:

The bot uses its item at its current location

>Failure to meet the specifications of the *Use Item* action (See Section F. *Items and Upgrades* for each item's required syntax) will result in no action being taken.

## E. Growing Plants

In order to grow crops you'll need seeds which can be purchased from the green grocer.

Each crop has a **growth_time** and **current value** parameter, and will grow over the course of *growth_time* turns. Its *current_value* begins at 0 and increases at the end of each turn based on the following formula:

>*current value* += *value_per_turn* * \[(1 - *fertility_sensitivity*) + (*tile_fertility* * *fertility_sensitivity*)]

Once *growth_time* turns have elapsed the crop will ripen, become harvestable, and no longer increase (or decrease) in value.

To explain the formula further:

**value_per_turn** is a property of the plant type, and represents the value the crop would gain each turn if it wasn't affected by the fertility band.

**tile_fertility** is a property of the tile the plant is on, and can change from one turn to the next. It represents how well-suited the tile is for plant growth.

**fertility_sensitivity** is a property of the plant type, and represents the proportion of the plant's *value_per_turn* that is multiplied by *tile_fertility*. 

To put the math into easier terms, a plant with a *fertility_sensitivity* of 0 is completely unaffected by the fertility band and will be worth *growth_time* * *value_per_turn* once it ripens, no matter what kind of tile it grows on. As *fertility_sensitivity* increases, the more affect the *tile_fertility* will have on the crop's final value.

### Plant List

#### **Crop**

<image src="images/PotatoHarvested.png" width="30px">  **Potato** 

Basic plant, takes time to grow, can???t use fertility, but can make you an honest living

<image src="images/CornHarvested.png" width="30px">  **Corn**

Can use fertility and grows quickly. Can???t turn a profit without using the fertility band though

<image src="images/GrapeHarvested.png" width="30px">  **Grape**

More expensive to plant, but sells for more. Can make minor use of fertility

<image src="images/JoganHarvested.png" width="30px">  **Jogan-Fruit**

Also expensive, but makes great use of the fertility band

<image src="images/PeanutHarvested.png" width="30px">  **Peanut**

Troll crop used to take up space on the other farmer???s side

<image src="images/WheatHarvested.png" width="30px">  **Quadrotriticale**

Turns a solid profit with the fertility band, and is fairly low risk,  takes a long time to grow though

<image src="images/DuchamHarvested.png" width="30px">  **Ducham-Fruit** 

Can turn an amazing profit using the fertility band, but very cost prohibitive to acquire.

<image src="images/GoldenHarvested.png" width="30px">  **Golden Corn**

A once in a lifetime opportunity, this incredibly expensive seed, when planted in the most fertile regions can turn an equally incredible profit.


| Crop | Fertility Sensitivity | Profit Margin | Price | Growth Time | Min Sell Value | Max Sell Value | Growth Value Per Turn |
| ---- | --------------------- | ------------- | ----- | ----------- | -------------- | -------------- | --------------------- |
| Potato | 0% | 110% | $5.00 | 4 turns | $5.50 | $5.50 | 1.375 |
| Corn | 50% | 100% | $5.00 | 2 turns | $2.50 | $6.25 | 2.500 |
| Grape | 50% | 125% | $15.00 | 10 turns | $8.25 | $18.73 | 1.650 |
| Jogan Fruit | 75% | 129% | $20.00 | 5 turns | $5.00 | $25.70 | 4.000 |
| Peanut | 0% | 100% | $5.00 | 30 turns | $5.00 | $5.00 | 0.167 |
| Quadrotritacale | 25% | 136% | $30.00 | 15 turns | $29.25 | $40.76 | 2.600 |
| Ducham Fruit | 100% | 100% | $100.00 | 5 turns | $0.00 | $138.00 | 20.00 |
| Golden Corn | 100% | 225% | $1000.00 | 3 turns | $0.00 | $2250.00 | 500.0 |


## F. Items and Upgrades

Each bot can take either 1 item of 1 upgrade with them for each match

Items can be activated to grant a powerful boost, but only one time per match

Upgrades increase the capabilities of the bot for the entire match but aren't as powerful as Items

### Item List

<image src="images/Rain Totem.png" width="30px">  **Rain Totem**

Cause each crop in a 5x5 square centered on the farmer???s position to grow up to three times this turn instead of only once.

<image src="images/Fertility Idol.png" width="30px">  **Fertility Idol**

Doubles the fertility of all tiles within radius 2 for the next growth step. 

<image src="images/Pesticide.png" width="30px">  **Pesticide**

Decrease the current value of all crops within 1 radius by 20%.

<image src="images/Scarecrow.png" width="30px">  **Scarecrow**

Protects tiles within radius 2 from harvest or planting by the opponent.(5x5)

<image src="images/Delivery Drone.png" width="30px">  **Delivery Drone**

Allows the farmer to buy crops and seeds from anywhere on the farm for one turn.

<image src="images/Coffee Thermos.png" width="30px">  **Coffee Thermos**

Triples the farmer???s movement speed for the next turn

### Upgrade List


<image src="images/Backpack.png" width="30px">  **Backpack**

Carrying capacity increased. (30 -> 50)

<image src="images/Scythe.png" width="30px">  **Scythe**

Harvest radius increased. (not plant radius though) (1 -> 5)

<image src="images/Green Grocer Loyalty Card.png" width="30px">  **Green Grocer Loyalty Card**

After spending $25 at the Green Grocer, buy seeds for 5% less.

<image src="images/Moon Shoes.png" width="30px">  **Longer Legs**

Movement speed increased. (10 -> 20)

<image src="images/Rabbit_s Foot.png" width="30px">  **Rabbit???s Foot**

Crops have a 5% chance of dropping twice when harvested.

<image src="images/Seed-a-Pult.png" width="30px">  **Seed-A-Pult**

Plant radius increased. (1 -> 5)

<image src="images/Spyglass.png" width="30px">  **Spyglass**

Increases the farmer's protection radius. (2 -> 3)

## G. Achievements

Achievements are fun goals to be completed across the entire tournament.

### Achievement List

<image src="images/A Worthy Heir.png" width="30px">  **A Worthy Heir**

Win a game.

<image src="images/My Favorite Customer.png" width="30px">  **My Favorite Customer**

Spend $1,000 at the Green Grocer in one game.

<image src="images/Seedy Business.png" width="30px">  **Seedy Business**

Harvest and sell a crop that another farmer planted.

<image src="images/Omni-Agriculturist.png" width="30px">  **Omni-Agriculturalist**

Sell at least one crop of each type besides peanuts and golden corn in a single game.

<image src="images/Not Worth the Dirt He Sows.png" width="30px">  **Not Worth the Dirt He Sows**

Finish a game with *less* money than you started with.

<image src="images/Dust Bowl.png" width="30px">  **Dust Bowl**

Have 10 of your crops destroyed by the fertility zone passing over them.

<image src="images/Grapes of Mild Displeasure.png" width="30px">  **Grapes of Mild Displeasure**

Steal and sell 5 of your opponent???s grape crops.

<image src="images/Ornithophobia.png" width="30px">  **Ornithophobia**

Place a scarecrow within the effect radius of the other farmer's scarecrow.

<image src="images/Stalks and Bonds.png" width="30px">  **Stalks and Bonds**

Sell a golden corn.

<image src="images/It Ain_t Much But It_s Honest Work.png" width="30px">  **It Ain't Much But It's Honest Work**

Win a game without ever harvesting a crop that your opponent planted.

<image src="images/Botanical Burglary.png" width="30px">  **Botanical Burglary**

Win a game without ever planting a crop

<image src="images/Fruits of our Labor.png" width="30px">  **Fruits of our Labor**

Win a game only planting, harvesting, and selling fruit crops (Ducham-Fruit, Jogan-Fruit, Grape).

<image src="images/Richer Than Phineas Himself.png" width="30px">  **Richer than Phineas Himself**

Have $2,500 or more in your account at the end of the game.

# Registration
For the competition, you???ll need to have the following things installed:

- Python 3. This is one of the languages you can use to implement your bot.

- Java 8. This will allow you to run the game engine locally, so you can test your code before you push it.

- [Node.js](https://nodejs.org/en/), (version 14.17.6). Once you have that installed, try running `npm i -g mechmania`. The package can be found [here](https://www.npmjs.com/package/mechmania), and when you???re able to run `mm help`, you know you'll have downloaded it correctly. From there, you can register a team by doing `mm register`. We ask that you only register once per team.

If you need help, please ask us in the Discord #ask-a-question-here channel. We'll be providing help throughout the competition!

For basic questions, please run `mm help` before asking.
