# Reflection

**Plugin Build Status:** <br />
[![Windows-x86_64](https://github.com/Brandon-T/RemoteInput/workflows/Windows-x86_64/badge.svg)](https://github.com/Brandon-T/Reflection/releases/latest)
[![Windows-i686](https://github.com/Brandon-T/RemoteInput/workflows/Windows-i686/badge.svg)](https://github.com/Brandon-T/Reflection/releases/latest)
<br />
[![MacOS-x86_64](https://github.com/Brandon-T/RemoteInput/workflows/MacOS-x86_64/badge.svg)](https://github.com/Brandon-T/Reflection/releases/latest)
[![Linux-x86_64](https://github.com/Brandon-T/RemoteInput/workflows/Linux-x86_64/badge.svg)](https://github.com/Brandon-T/Reflection/releases/latest)
<br />
[![Linux-armhf](https://github.com/Brandon-T/RemoteInput/workflows/Linux-armhf/badge.svg)](https://github.com/Brandon-T/Reflection/releases/latest)
[![Linux-aarch64](https://github.com/Brandon-T/RemoteInput/workflows/Linux-aarch64/badge.svg)](https://github.com/Brandon-T/Reflection/releases/latest)


**Notes:**
- Includes Player Functions
- Includes NPC Functions
- Includes Model Rendering & Animations

Work In Progress..

Example Usage:
```pascal
{$i reflection/reflection.simba}


{ Finds the mid point of an object's model }
Function RSObject.MidPoint: TPoint;
var
  model: RSModel;
  triangles: Array of T2DTriangle;
  points: TPointArray;
  i: Int32;
begin
  model := self.Model;
  triangles := model.Project(localtile.X, localtile.Y, localtile.GetHeight(), -1);
  model.Free;

  for i := 0 to high(triangles) do
  begin
    points += triangles[i].A;
    points += triangles[i].B;
    points += triangles[i].C;
  end;

  Result := MiddleTPA(points);
end;

{ Finds the Convex Hull of a model }
Function RSObject.Convex: Array of TPoint;
var
  model: RSModel;
  triangles: Array of T2DTriangle;
  points: TPointArray;
  i: Int32;
begin
  model := self.Model;
  triangles := model.Project(localtile.X, localtile.Y, localtile.GetHeight(), -1);
  model.Free;

  for i := 0 to high(triangles) do
  begin
    points += triangles[i].A;
    points += triangles[i].B;
    points += triangles[i].C;
  end;

  Result := ConvexHull(points);
end;

{ Find's any object a player within some radius }
Function RSPlayer.FindObjectsInRadius(Radius: Int32): array of RSObject;
var
  X, Y: Int32;
  BaseX, BaseY, Plane: Int32;
  Region: RSRegion;
  Tile: RSSceneTile;
  Garbage: RSTypeArray;
begin
  if Radius >= 104 then
    Exit;

  Region := RSClient.Region;
  if Region.ref = nil then
    Exit;

  BaseX := self.Tile.X - RSClient.BaseX;
  BaseY := self.Tile.Y - RSClient.BaseY;
  Plane := RSClient.Plane;

  for Y := BaseY - Radius to BaseY + Radius do
  begin
    for X := BaseX - Radius to BaseY + Radius do
    begin
      Tile := Region.SceneTile(Plane, X, Y);
      if Tile.ref <> nil then
      begin
        Result += Tile.GameObject;
        Garbage += Tile;
      end;
    end;
  end;

  Garbage.Free;
  Region.Free;
end;

{ Find's any by ID within some Radius from my player }
Function RSPlayer.FindObjectsByID(ID, Radius: Int32): array of RSObject;
var
  I: Int32;
  Objects, Garbage: Array of RSObject;
begin
  Objects := self.FindObjectsInRadius(Radius);

  For I := 0 to high(Objects) do
    if Objects[I].ID = ID then
      Result += Objects[I]
    else
      Garbage += Objects[I];

  RSTypeArray(Garbage).Free;
end;


{ The main part of your script.. all logic goes here.. }
Procedure ScriptMain(DebugBitmap: TMufasaBitmap; Me: RSLocalPlayer);
var
  I: Int32;
  NPCs: Array of RSNPC;
  NPCDefinition: RSNPCDefinition;
  Inventory: Array of TRSInventoryItem;
  Players: Array of TRSPlayer;
  Messages: Array of TRSChatMessage;
begin
  writeln('About Me: ');
  writeln('My Name is: ', Me.Name);
  writeln('My Combat Level is: ', Me.CombatLevel);
  writeln('My Location is: ', Me.Tile);
  writeln('My Health is: ', Me.HealthRatio);
  writeln('Am I Animating? ', Me.IsAnimating);
  writeln('My Animation ID: ', Me.AnimationId);
  writeln('');
  writeln('----');
  writeln('');

  writeln('NPCs Near Me: ');
  NPCs := RSNPC.AllNPCs;

  For I := 0 to high(NPCs) do
  begin
    NPCDefinition := NPCs[I].Definition;
    writeln('NPC ID: ', NPCDefinition.ID);
    writeln('NPC Name: ', NPCDefinition.Name);
    writeln('NPC Combat Level: ', NPCDefinition.CombatLevel);
    writeln('NPC Location: ', NPCs[I].Tile);
    writeln('NPC Animation ID: ', NPCs[I].AnimationId);
    writeln('');
    NPCDefinition.Free;
  end;
  RSTypeArray(NPCs).Free;

  writeln('-----');
  writeln('');

  writeln('My Inventory Contains: ');
  Inventory := R_GetAllInventoryItems;
  for I := 0 to high(Inventory) do
  begin
    writeln(Inventory[I]);
  end;

  writeln('');
  writeln('-----');
  writeln('');

  writeln('My Bank Is Open: ', R_BankScreen);
  writeln('Is a Deposit Box Open: ', R_DepositBox);

  //If my bank is open..
  if R_BankScreen then
  begin
    writeln('Does my Bank contain leather? ', R_FindBankItem(1741));
    R_WithdrawItem(1741, 1); //Withdraw ONE "Leather" from the bank if it exists.

    R_BankScreen_Close; //Close the bank..
  end;

  writeln('');
  writeln('-----');
  writeln('');

  writeln('My Info: ');
  writeln(R_GetMe);

  writeln('');
  writeln('-----');
  writeln('');

  writeln('Other Players: ');
  Players := R_GetPlayers;
  for I := 0 to high(Players) do
  begin
    writeln(Players[I]);
  end;

  writeln('');
  writeln('-----');
  writeln('');

  writeln('My Skills: ');
  writeln('My Mining Level: ', Me.SkillLevel(R_SKILL_MINING));
  writeln('My Smithing Level: ', Me.SkillLevel(R_SKILL_SMITHING));
  writeln('My Attack Level: ', Me.SkillLevel(R_SKILL_ATTACK));
  writeln('My Fishing Level: ', Me.SkillLevel(R_SKILL_FISHING));
  writeln('My Total Level: ', Me.SkillLevel(R_SKILL_TOTALLEVEL));
  writeln('My Mining Experience: ', Me.SkillExperience(R_SKILL_TOTALLEVEL));

  writeln('');
  writeln('-----');
  writeln('');

  writeln('Current Game Tab: ', R_CurrentGameTab);
  writeln('Combat Tab Bounds: ', R_GameTabBounds(GAMETAB_COMBAT));

  writeln('');
  writeln('-----');
  writeln('');

  //Get the last 10 messages.. 0 = unlimited..
  writeln('Chat Messages:');
  Messages := R_ChatMessages(10);
  For I := 0 to high(Messages) do
  begin
    writeln(Messages[I]);
  end;

  writeln('');
  writeln('-----');
  writeln('');

  //When an NPC such as a banker is talking to you, and you want to choose an option..
  R_ChatChooseOption(['Make Fire', 'Make', 'ke Fi'], True);

  //When skilling, you get a dialog in the chat and want to click it..
  R_ChatSkillChooseOptions(3); //Choose option 3..
  R_ChatSkillChooseOptions(['Make a fire', 'ake fir', 'fire', 'etc'], True);

  DebugBitmap.DrawPlayer(Me, false, $FF); //Draw my player in red..
end;

var
  DebugBitmap: TMufasaBitmap;
  Me: RSPlayer;
  W, H: Int32;
  I: Int32;
  Players: Array of RSPlayer;
begin
  srl.Setup([]);
  ClearDebug;

  GetClientDimensions(W, H);
  EIOS_SetGraphicsDebugging(R_EIOS, True); //Enable drawing.. for debugging..

  DebugBitmap.Init;
  DebugBitmap.SetPersistentMemory(PtrUInt(EIOS_GetDebugImageBuffer(R_EIOS)), W, H);

  Me := RSPlayer.Me;
  ScriptMain(DebugBitmap, Me);
  Me.Free;
  DebugBitmap.Free;
end.
```
