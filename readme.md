# Minecraft exploits and how to fix them

## Preface
This guide will assume you are running [Purpur](https://purpurmc.org). Some settings required for this guide to work are
not available in Paper or Spigot. Similar exploits might exist in vanilla, fabric or forge servers, and you will have to
find mods that fix them on those loaders.


## Exploits

### Command spam

While even spigot will protect you from this exploit, there's a slight oversight that will enable a single command to be
usable to perform this one. To fix this, simply remove /skill command from the spam exclusions list in spigot.yml.

`spigot.yml`
```yaml
commands:
  spam-exclusions: []
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

Alternatively, you can keep the maps enabled, but set up vanilla worldborder and make sure your world is pregenerated
within the border. Then you can just make sure that already discovered treasures are valid as the search result. This 
will prevent the treasure search from loading chunks that are not generated.

`paper-world-defaults.yml`
```yaml
  treasure-maps:
    find-already-discovered:
      loot-tables: true
      villager-trade: true
```
