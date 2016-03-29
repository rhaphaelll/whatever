if GetObjectName(GetMyHero()) ~= "Irelia" then return end
 
require("Inspired")
require("OpenPredict")
 
--Irelia Menu--
local IreliaMenu = MenuConfig("Irelia","Irelia")
IreliaMenu:SubMenu("Combo","Combo")
-- IreliaMenu:SubMenu("Harass","Harass")
IreliaMenu:SubMenu("LaneClear","LaneClear")
IreliaMenu:SubMenu("LastHit","LastHit")
--
-- IreliaMenu.Combo:DropDown("QMode","Q Mode",1,{Simple})
IreliaMenu.Combo:Boolean("UseQ","Use Q",true)
IreliaMenu.Combo:Boolean("UseW","Use W",true)
IreliaMenu.Combo:Boolean("UseE","Use E",true)
IreliaMenu.Combo:Boolean("UseR","Use R",true)
IreliaMenu.Combo:DropDown("SheenMode","Sheen Mode",1,{"No Sheen","Use Sheen","Force Sheen"})
--
IreliaMenu.LaneClear:Boolean("UseQ","Use Q",true)
IreliaMenu.LaneClear:Boolean("UseW","Use W",true)
IreliaMenu.LaneClear:Boolean("UseE","Use E",true)
IreliaMenu.LaneClear:Boolean("UseR","Use R",false)
--
IreliaMenu.LastHit:Boolean("UseQ","Use Q",true)
IreliaMenu.LastHit:Boolean("UseE","Use E",true)
--
 
--Local Variables--
local Target = GetCurrentTarget()
local SheenActive = nil
local SheenOffCD = true
local HitenStyle = nil
local UseSpell = true
 
--Irelia Damages--
local QDmg = CalcDamage(GetMyHero(),Target,30*GetCastLevel(myHero,_Q)-10,0)+GetBaseDamage(myHero)+GetBonusDmg(myHero)
local WDmg = CalcDamage(GetMyHero(),Target,15*GetCastLevel(myHero,_W),0)
local EDmg = CalcDamage(GetMyHero(),Target,0,40+40*GetCastLevel(myHero,_E))+0.5*GetBonusAP(myHero)
local RDmg = CalcDamage(GetMyHero(),Target,40+40*GetCastLevel(myHero,_R),0)+0.6*(GetBaseDamage(myHero)+GetBonusDmg(myHero))+0.5*GetBonusAP(myHero)
 
--Irelia Skills--
local IreliaR = {speed = 1700, delay = 0.250, range = 1200, width = 25}
 
--Orbwalker--
OnTick(function(myHero)
if IOW:Mode() == "Combo" then
SheenMode()
SimpleCombo()
end
if IOW:Mode() == "LaneClear" then
LaneClear()
end
if IOW:Mode() == "LastHit" then
LastHit()
end
end)
 
--Sheen Check--
OnUpdateBuff(function (unit,buff)
if not unit or not buff then
return
end
if unit.isMe then
if buff.Name:lower() == "sheen" then
SheenActive = true
SheenOffCD = false
DelayAction(function() SheenOffCD = true end,1.5)
end
end
end)
 
OnRemoveBuff(function(unit,buff)
if not unit or not buff then
return
end
if unit.isMe then
if buff.Name:lower() == "sheen" then
SheenActive = false
end
end
end)
 
--Hiten Style Check--
OnUpdateBuff(function(unit,buff)
if not unit or not buff then
return
end
if unit.isMe then
if buff.Name:lower() == "ireliahitenstylecharged" then
HitenStyle = true
end
end
end)
 
OnRemoveBuff(function(unit,buff)
if not unit or not buff then return end
if unit.isMe then
if buff.Name:lower() == "ireliahitenstylecharged" then
HitenStyle = false
end
end
end)
 
--Sheen Mode--
function SheenMode()
if UseSpell then return end
if not UseSpell then
if IreliaMenu.Combo.SheenMode:Value() == 1 then
UseSpell = true
elseif IreliaMenu.Combo.SheenMode:Value() == 2 then
if SheenActive and GetDistance(myHero,Target) < 125 then
UseSpell = false
elseif SheenActive  and GetDistance(myHero,Target) > 125 then
UseSpell = true
elseif not SheenActive then
UseSpell = true
end 
elseif IreliaMenu.Combo.SheenMode:Value() == 3 then
if SheenActive and GetDistance(myHero,Target) < 125 and SheenOffCD then
UseSpell = false
elseif SheenActive and GetDistance(myHero,Target) > 125 and SheenOffCD then
UseSpell = true
elseif SheenActive and GetDistance(myHero,Target) < 125 and not SheenOffCD then
UseSpell = false
elseif SheenActive and GetDistance(myHero,Target) > 125 and not SheenOffCD then
if GetDistance(myHero,Target) < 425 then
UseSpell = false
elseif GetDistance(myHero,Target) < 650 then
UseSpell = true
end
elseif not SheenActive then
UseSpell = true
end
end 
end
end
 
--Simple Combo Mode--
function SimpleCombo()
local Target = GetCurrentTarget()
if IreliaMenu.Combo.UseQ:Value() and Ready(_Q) and ValidTarget(Target,650) and UseSpell then
CastTargetSpell(Target,_Q)
UseSpell = false 
end
if IreliaMenu.Combo.UseW:Value() and Ready(_W) and ValidTarget(Target,425) and UseSpell then
CastSpell(_W)
UseSpell = false 
end
if IreliaMenu.Combo.UseE:Value() and Ready(_E) and ValidTarget(Target,425) and UseSpell then
CastTargetSpell(Target,_E)
UseSpell = false 
end
if IreliaMenu.Combo.UseR:Value() and Ready(_R) and ValidTarget(Target,1600) and UseSpell then
local RPredict = GetPrediction(Target,IreliaR)
if RPredict and RPredict.hitChance >= 0.75 then
CastSkillShot(_R,RPredict.castPos)
UseSpell = false
end
end
end
 
--Lane Clear Mode--
function LaneClear()
for i,Minion in pairs(minionManager.objects) do
local FarmPos = GetLineFarmPosition(1600,25,MINION_ENEMY)
if not HitenStyle and ValidTarget(Minion,125) and MinionsAround(GetOrigin(myHero),600) > 3 and IreliaMenu.LaneClear.UseW:Value() then
CastSpell(_W)
end
if HitenStyle then
if ValidTarget(Minion,425) and Ready(_E) and IreliaMenu.LaneClear.UseE:Value() and EDmg + WDmg > GetCurrentHP (Minion) then
CastTargetSpell(Minion,_E)
end
if ValidTarget(Minion,650) and Ready(_Q) and IreliaMenu.LaneClear.UseQ:Value() and QDmg + WDmg > GetCurrentHP (Minion) then
CastTargetSpell(Minion,_Q) 
end
elseif not HitenStyle then
if ValidTarget(Minion,425) and Ready(_E) and IreliaMenu.LaneClear.UseE:Value() and EDmg > GetCurrentHP (Minion) then
CastTargetSpell(Minion,_E)
end
if ValidTarget(Minion,650) and Ready(_Q) and IreliaMenu.LaneClear.UseQ:Value() and QDmg > GetCurrentHP (Minion) then
CastTargetSpell(Minion,_Q)
end
end
if FarmPos > 3 and Ready(_R) and IreliaMenu.LaneClear.UseR:Value() then
CastSkillShot(_R,FarmPos)
end
end
end
 
--Last Hit Mode--
function LastHit()
for i,Minion in pairs(minionManager.objects) do
if GetDistance(myHero,Minion) < 400 then
return
end
if GetDistance(myHero,Minion) > 400 then
if HitenStyle then
if ValidTarget(Minion,425) then
if Ready(_E) and IreliaMenu.LastHit.UseE:Value() and EDmg + WDmg > GetCurrentHP (Minion) then
CastTargetSpell (Minion,_E)
elseif not Ready(_E) and Ready(_Q) and IreliaMenu.LastHit.UseQ:Value() and QDmg + WDmg > GetCurrentHP (Minion) then
CastTargetSpell (Minion, _Q)
end
elseif ValidTarget(Minion,650) then
if Ready(_Q) and IreliaMenu.LastHit.UseQ:Value() and QDmg + WDmg > GetCurrentHP(Minion) then
CastTargetSpell(Minion,_Q)
end
end
elseif not HitenStyle then
if ValidTarget(Minion,425) then
if Ready(_E) and IreliaMenu.LastHit.UseE:Value() and EDmg > GetCurrentHP (Minion) then
CastTargetSpell (Minion,_E)
elseif not Ready(_E) and Ready(_Q) and IreliaMenu.LastHit.UseQ:Value() and QDmg > GetCurrentHP(Minion) then
CastTargetSpell(Minion,_Q)
end
elseif ValidTarget(Minion,650) then
if Ready(_Q) and IreliaMenu.LastHit.UseQ:Value() and QDmg > GetCurrentHP(Minion) then
CastTargetSpell(Minion,_Q)
end
end 
end
end
end
end
