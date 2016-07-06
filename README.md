
# Diablo II Save File Format

## Header

Each .d2s file starts with a 765 byte header, after which data
is of variable length.

Byte | Length | Desc
-----|--------|------------
0    | 4      | Signature (0xaa55aa55)
4    | 4      | [Version ID](#versions)
8    | 4      | File size
12   | 4      | [Checksum](#checksum)
16   | 4      | ?
20   | 16     | Character Name (ASCII padded with 0 bytes)
36   | 1      | [Character Status](#character-status)
37   | 1      | [Character Progression](#Character-progression)
38   | 2      | ?
40   | 1      | [Character Class](#character-class)
41   | 2      | ?
43   | 1      | Level
44   | 4      | ?
48   | 4      | Time
52   | 4      | ?
56   | 64     | [Hotkeys](#hotkeys)
120  | 4      | Left Mouse
124  | 4      | Right Mouse
128  | 4      | Left Mouse (weapon switch)
132  | 4      | Right Mouse (weapon switch)
136  | 32     | ?
168  | 3      | [Difficulty](#difficulty)
171  | 4      | [Map](#map)
175  | 2      | ?
177  | 2      | Merc dead?
179  | 4      | Merc seed?
183  | 2      | Merc Name ID
185  | 2      | Merc Type
187  | 4      | Merc Experience
191  | 144    | ?
335  | 298    | [Quest](#quest)
633  | 81     | [Waypoint](#waypoint)
714  | 51     | [NPC](#npc)
765  |        | [Stats](#stats)
     |        | [Items](#items)

### Versions

File version. The following values are known:

* `71` is 1.00 through v1.06
* `87` is 1.07 or Expansion Set v1.08
* `89` is standard game v1.08
* `92` is v1.09 (both the standard game and the Expansion Set.)
* `96` is v1.14

### Checksum

To calculate the checksum set the value of it in the .d2s data
to be zero and iterate through all the bytes in the data calculating
a 32-bit checksum:

    sum = (sum << 1) + data[i];

### Character Status

This is a 8-bit field:

Bit | Desc
----|------
0   | ?
1   | ?
2   | Hardcore
3   | Died
4   | ?
5   | Expansion
6   | ?
7   | ?

### Character Progression

TODO

### Character Class

ID | Class
---|-------
0  | Amazon
1  | Sorceress
2  | Necromancer
3  | Paladin
4  | Barbarian
5  | Druid
6  | Assassin

### Hotkeys

TODO

### Difficulty

TODO

### Map

TODO

### Quest

TODO

### Waypoint

TODO

### NPC

TODO


### Stats

TODO (9-bit encoding)


### Items

Items are stored in lists described by this header:

Byte | Size | Desc
-----|------|-----
0    | 2    | "JM"
2    | 2    | Item Count

After this come N items. Each item starts with a basic 14-byte
structure. Many fields in this structure are not "byte-aligned"
and are described by their bit position and sizes.

Bit | Size | Desc
----|------|------
0   | 16   | "JM" (separate from the list header)
16  | 4    | ?
20  | 1    | Identified
21  | 6    | ?
27  | 1    | Socketed
28  | 1    | ?
29  | 1    | Picked up since last save
30  | 2    | ?
32  | 1    | Ear
33  | 1    | Starter Gear
34  | 3    | ?
37  | 1    | Compact
38  | 1    | Ethereal
39  | 1    | ?
40  | 1    | Personalized
41  | 1    | ?
42  | 1    | [Runeword](#runeword)
43  | 15   | ?
58  | 3    | [Parent](#parent)
61  | 4    | [Equipped](#equipped)
65  | 4    | Column
69  | 3    | Row
72  | 1    | ?
73  | 3    | [Stash](#parent)
76  | 4    | ?
80  | 24   | Type code (3 letters)
108 |      | [Extended Item Data](#extended-item-data)

### Extended Item Data

If the item is marked as `Compact` (bit 37 is set) no extended
item information will exist and the item is finished.

Items with extended information store bits based on information in the item header. For example, an item marked as `Socketed` will store an
extra 3-bit integer encoding how many sockets the item has.

Bit | Size | Desc
----|------|-------
108 |      | [Sockets](#sockets)
    |      | [Custom Graphics](#custom-graphics)
    |      | [Class Specific](#class-specific)
    |      | [Quality](#quality)
    |      | [Mods](#mods)

### Custom Graphics

Custom graphics are denoted by a single bit, which if
set provides a 3-bit number for alternate graphics.

Bit | Size | Desc
----|------|------
0   | 1    | Item has custom graphics
1   | 3    | Alternate graphic index

### Class Specific

Class items like Barbarian helms or Amazon bows have special
properties specific to those kinds of items. If the first bit
is empty the remaining 11 bits will not be present.

Bit | Size | Desc
----|------|------
0   | 1    | Item has class specific data
1   | 11   | Class specific bits

### Quality

Item quality is encoded as a 4-bit integer.

#### Low Quality


### Mods

After each item is a list of mods. The list is a series of key value
pairs where the key is a 9-bit number and the value depends on the key.
The list ends when key `511` (`0x1ff`) is found which is all 9-bits
being set.

 matching the `code` from the
`ItemStatCost.txt` file. The value is N bits described by the `Save Bits`
and `Param Bits` fields of the text file.

#### Runeword

TODO

#### Parent

All items are located somewhere and have a "parent" which
can be another item, such as when inserting a jewel.

Value | Desc
------|------
0     | Stored
1     | Equipped
2     | Belt
4     | Cursor
6     | Item

For items that are "stored" a 3-bit integer encoded starting at
bit 73 describes where to store the item:

Value | Desc
------|------
1     | Inventory
4     | Horadric Cube
5     | Stash

### Equipped

Items that are equipped describe their slot:

Value | Slot
------|-----
1     | Helmet
2     | Amulet
3     | Armor
4     | Weapon (Right)
5     | Weapon (Left)
6     | Ring (Right)
7     | Ring (Left)
8     | Belt
9     | Boots
10    | Gloves
11    | Alternate Weapon (Right)
12    | Alternate Weapon (Left)

### Sockets