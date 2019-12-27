# Reflection

Work In Progress..

Example Usage:
```pascal
{$i Reflection/Reflection.simba}

var
  player: RSPlayer;
begin
  RSetup(6440);

  player := RSPlayer.Me;
  writeln(player.Name);

  RFreeObject(R_EIOS, player.ref);
end.
```
