# Reflection

- Includes Player Functions
- Includes NPC Functions
- Includes Model Rendering & Animations

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
  player.Free;
end.
```

```pascal
{$i Reflection/Reflection.simba}

var
  item: TRSBankItem;
begin
  RSetup(6440);

  item := R_FindBankItem(1024);
  if (item.isValid) then
  begin
    Mouse(item, R_RIGHTCLICK);
  end;
end.
```


```pascal
{$i Reflection/Reflection.simba}

var
  item: TRSBankItem;
begin
  RSetup(6440);

  writeln(R_GetMMLevels(MM_LEVEL_HITPOINTS));
end.
```
