# Pixelmon Loot Tables
This project aims to provide [Pixelmon](http://pixelmonmod.com/) loot tables for
Minecraft version 1.10.2. Basically, it takes advantage of the [loot
tables](http://minecraft.gamepedia.com/Loot_table) feature added in Minecraft
1.9, to add Pixelmon items to loot chests generated in the world.

Pixelmon for Minecraft versions 1.8 to 1.8.9 had an optional sidemod called
[ItemFinder](http://pixelmonmod.com/wiki/index.php?title=ItemFinder_(sidemod)).
According to Pixelmon developers, loot tables render the need for the sidemod
obsolete.

The current state of this project roughly emulates the results of the original
ItemFinder sidemod. However, due to the difference between the loot table
implementation and the original Forge APIs, it is very difficult (if not
impossible) to get an exact match. In the future, it would be nice to further
balance the loot tables and include a larger variety of Pixelmon items.


## Pixelmon Items
These loot tables add the following Pixelmon items to various chests:
- Poke Balls
- Evolution Stones
- Rare Candies


## Download and Install
Empty world downloads containing the loot tables can be found in
[releases](https://github.com/BrowncoatShadow/pixelmon-loot-tables/releases).

### Existing World
Unzip and copy the `data/` directory into your world folder. Choose to merge if
prompted.

### New World
Unzip and copy the `world/` directory to where your worlds are saved.
- **Server Multiplayer:** Rename the directory to match the `level-name` from
  your `server.properties` file.
- **Singleplayer:** Rename the directory to match the name of the single player
  world you are going to create in your Minecraft client.


## Building and Installing From Source
### Requirements
- `ruby >= 2.0.0`
- `rake` Ruby Gem
- `zip` and `unzip` binaries (*Ships with macOS and most linux distros.*)

### Build and Install
```
rake
rake install WORLD_NAME="Pixelmon World"
```
The `install` and `package` rake tasks consume the environment variable
`WORLD_NAME`, which defaults to "world" if not set. You can name it whatever you
like, but it should match the name of the world you create in the Minecraft
client, so it uses the correct folder.

There are a number of useful rake tasks; get a full list with `rake -T`.

### Build Process
The build process does a number of things:
1. Download the vanilla Minecraft Server JAR and extract the default loot
   tables.
2. Take the loot tables defined in `config.yml` and create a loot pool
   containing the specified Pixelmon items, using the item JSON objects defined
   in the `lib/` directory.
3. Place newly build loot tables into a directory tree contained in `data/`.
4. The resulting `data/` directory can either be installed to a world save
   folder or packaged into a zip file containing an empty world folder, using
   the `install` and `package` rake tasks.


## Contributing
This project was originally created to fulfill my personal needs. If you find it
useful and want to help improve it, I will gladly accept contributions. There
are a number of things that could be done to improve this project.
- Support for multiple loot pools.
- Better balance of weights, quantities, variety, etc.
- Possibly modifying loot pools of vanilla items.
