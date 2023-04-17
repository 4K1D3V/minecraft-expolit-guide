# Minecraft exploits and how to fix them

## Preface
This guide will assume you are running [Purpur](https://purpurmc.org). Some settings required for this guide to work are
not available in Paper or Spigot. Similar exploits might exist in vanilla, fabric or forge servers, and you will have to
find mods that fix them on those loaders.


## Exploits

### Armor stand lag machines

Armor stands can be used to create lag machines. This is done by placing huge amounts of armor stands and forcing them
to move via water, pistons or other means. This can be fixed by disabling armor stand tick and collision lookups. This
will cause armor stands to not being able to be pushed or pulled (even by gravity).

`paper-world-defaults.yml`
```yaml
entities:
  armor-stands:
    do-collision-entity-lookups: false
    tick: false
```

### Collision lag machines

This exploit is similar to the armor stand one, but instead of armor stands, it uses entities that can be pushed by
other entities. This can be fixed by setting a smaller limit on how many entities can collide with a singular entity.
You can set the max-entity-collisions to 2 to still have relatively natural behavior, or set it to 0 to completely
disable collisions.

`paper-world-defaults.yml`
```yaml
collisions:
  max-entity-collisions: 2
```

### Command spam

While even spigot will protect you from this exploit, there's a slight oversight that will enable a single command to be
usable to perform this one. To fix this, simply remove /skill command from the spam exclusions list in spigot.yml.

`spigot.yml`
```yaml
commands:
  spam-exclusions: []
```

### Join spam

Sometimes shear quantity of players joining the server can cause the server to lag out. This is especially true for bot 
attacks and moments after server restart. This can be fixed by setting max joins per tick. Players joining will be
delayed in time so server can properly tick between handling them. If joins still overwhelm the server, you can also
enable max-joins-per-second in purpur.yml that will make it so value from paper config will be applied per second
instead of per tick.

`paper-global.yml`
```yaml
misc:
  max-joins-per-tick: 3
```

`purpur.yml`
```yaml
settings:
  network:
    max-joins-per-second: true
```
### Treasure search

When new treasure map is generated, usually via cartographer villager or opening a chest with treasure map in it, the
server searches for the treasure that map should lead to. This search is done in a way that in most cases causes a lot
of chunks loading and possibly generating them. This search can halt the server for long enough that watchdog process 
kills it. It can also be triggered by feeding dolphins fish.

#### Option 1

To fix this one you basically have to disable treasure maps and dolphins searching for treasure. This is the recommended
and 100% effective solution.

`paper-world-defaults.yml`
```yaml 
environment:
  treasure-maps:
    enabled: false
```

`purpur.yml`
```yaml
world-settings:
  default:
    mobs:
      dolphin:
        disable-treasure-searching: true
```

#### Option 2

Alternatively, you can keep the maps enabled, but set up vanilla world border and make sure your world is pregenerated
within the border. Then you can just make sure that already discovered treasures are valid as the search result. This 
will prevent the treasure search from loading chunks that are not generated.

`paper-world-defaults.yml`
```yaml
  treasure-maps:
    find-already-discovered:
      loot-tables: true
      villager-trade: true
```
