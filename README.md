-- Trade Value Checker (Reworked v8.9 - Fixed)
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local DEBUG = false

-- =====================================================================
-- КОЛІР → RARITY
-- =====================================================================
local function colorToRarity(c)
	if not c then return nil end
	local r = math.floor(c.R * 255 + 0.5)
	local g = math.floor(c.G * 255 + 0.5)
	local b = math.floor(c.B * 255 + 0.5)

	-- ЗЕЛЕНИЙ = Rare (ловимо будь-який зелений відтінок)
	if g > 100 and g > r + 50 and g > b + 50 then
		return "Rare"
	end

	-- Godly — рожево-малиновий
	if r >= 180 and g <= 90 and b >= 120 then
		return "Godly"
	end

	-- Legendary — темно-червоний
	if r >= 150 and g <= 90 and b <= 90 then
		return "Legendary"
	end

	-- Uncommon — блакитний
	if r <= 100 and g >= 180 and b >= 180 then
		return "Uncommon"
	end

	-- Common — сірий
	if math.abs(r - g) <= 30
		and math.abs(g - b) <= 30
		and math.abs(r - b) <= 30
		and r >= 50 and r <= 180 then
		return "Common"
	end

	return nil
end

-- =====================================================================
-- БАЗА ЗБРОЇ
-- =====================================================================
local ITEM_DATABASE = {
	-- LEGENDARY
		-- LEGENDARY
	["latte (gun)"]        = { value = 140, rarity = "Legendary" },
	["latte (knife)"]      = { value = 140, rarity = "Legendary" },
	["spectral (knife)"]   = { value = 50,  rarity = "Legendary" },
	["traveler (gun)"]     = { value = 50,  rarity = "Legendary" },
	["aurora (gun)"]       = { value = 45,  rarity = "Legendary" },
	["vampire (gun)"]      = { value = 45,  rarity = "Legendary" },
	["beach"]              = { value = 35,  rarity = "Legendary" },
	["cotton candy"]       = { value = 35,  rarity = "Legendary" },
	["jd"]                 = { value = 27,  rarity = "Legendary" },
	["bunnies"]            = { value = 15,  rarity = "Legendary" },
	["arctic (gun)"]       = { value = 10,  rarity = "Legendary" },
	["golden"]             = { value = 10,  rarity = "Legendary" },
	["shadow"]             = { value = 10,  rarity = "Legendary" },
	["cavern (knife)"]     = { value = 7,   rarity = "Legendary" },
	["broken"]             = { value = 7,   rarity = "Legendary" },
	["icedriller"]         = { value = 5,   rarity = "Legendary" },
	["nightsky"]           = { value = 5,   rarity = "Legendary" },
	["ghost (knife)"]      = { value = 5,   rarity = "Legendary" },
	["ginger (gun)"]       = { value = 5,   rarity = "Legendary" },
	["aurora (knife)"]     = { value = 3,   rarity = "Legendary" },
	["emberknife"]         = { value = 3,   rarity = "Legendary" },
	["spectral (gun)"]     = { value = 3,   rarity = "Legendary" },
	["traveler (knife)"]   = { value = 3,   rarity = "Legendary" },
	["vampire (knife)"]    = { value = 3,   rarity = "Legendary" },
	["witched"]            = { value = 3,   rarity = "Legendary" },
	["blue elite"]         = { value = 3,   rarity = "Legendary" },
	["green elite"]        = { value = 3,   rarity = "Legendary" },
	["red scratch"]        = { value = 3,   rarity = "Legendary" },
	["santa's magic"]      = { value = 3,   rarity = "Legendary" },
	["santa's spirit"]     = { value = 3,   rarity = "Legendary" },
	["skulls"]             = { value = 3,   rarity = "Legendary" },
	["energized (gun)"]    = { value = 2,   rarity = "Legendary" },
	["frostfade (knife)"]  = { value = 2,   rarity = "Legendary" },
	["blue scratch"]       = { value = 2,   rarity = "Legendary" },
	["ghost (gun)"]        = { value = 2,   rarity = "Legendary" },
	["chromatic (knife)"]  = { value = 1,   rarity = "Legendary" },
	["icecracker"]         = { value = 1,   rarity = "Legendary" },
	["red fire"]           = { value = 1,   rarity = "Legendary" },
	["cavern (gun)"]       = { value = 1,   rarity = "Legendary" },
	-- RARE
    ["cane (knife)"]        = { value = 120, rarity = "Legendary" },
-- GODLY
	["traveler's gun"]     = { value = 4300, rarity = "Godly" },
	["evergun"]            = { value = 3625, rarity = "Godly" },
	["evergreen"]          = { value = 3000, rarity = "Godly" },
	["constellation"]      = { value = 2550, rarity = "Godly" },
	["turkey"]             = { value = 1950, rarity = "Godly" },
	["vampire's gun"]      = { value = 1800, rarity = "Godly" },
	["alienbeam"]          = { value = 1875, rarity = "Godly" },
	["raygun"]             = { value = 1850, rarity = "Godly" },
	["darkshot"]           = { value = 1450, rarity = "Godly" },
	["darksword"]          = { value = 1425, rarity = "Godly" },
	["blossom"]            = { value = 1160, rarity = "Godly" },
	["sakura"]             = { value = 1150, rarity = "Godly" },
	["snowcannon"]         = { value = 900,  rarity = "Godly" },
	["sunrise"]            = { value = 800,  rarity = "Godly" },
	["bauble"]             = { value = 825,  rarity = "Godly" },
	["sunset"]             = { value = 650,  rarity = "Godly" },
	["soul"]               = { value = 480,  rarity = "Godly" },
	["spirit"]             = { value = 470,  rarity = "Godly" },
	["rainbow gun"]        = { value = 405,  rarity = "Godly" },
	["flora"]              = { value = 400,  rarity = "Godly" },
	["rainbow"]            = { value = 400,  rarity = "Godly" },
	["xenoknife"]          = { value = 400,  rarity = "Godly" },
	["xenoshot"]           = { value = 400,  rarity = "Godly" },
	["bloom"]              = { value = 395,  rarity = "Godly" },
	["heart wand"]         = { value = 340,  rarity = "Godly" },
	["blizzard"]           = { value = 250,  rarity = "Godly" },
	["snowstorm"]          = { value = 250,  rarity = "Godly" },
	["ocean"]              = { value = 240,  rarity = "Godly" },
	["flowerwood gun"]     = { value = 240,  rarity = "Godly" },
	["waves"]              = { value = 235,  rarity = "Godly" },
	["flowerwood"]         = { value = 235,  rarity = "Godly" },
	["snow dagger"]        = { value = 165,  rarity = "Godly" },
	["treat"]              = { value = 155,  rarity = "Godly" },
	["sweet"]              = { value = 150,  rarity = "Godly" },
	["bat"]                = { value = 160,  rarity = "Godly" },
	["embersickle"]        = { value = 125,  rarity = "Godly" },
	["borealis"]           = { value = 130,  rarity = "Godly" },
	["icecream"]           = { value = 130,  rarity = "Godly" },
	["watergun"]           = { value = 130,  rarity = "Godly" },
	["australis"]          = { value = 125,  rarity = "Godly" },
	["immortal"]           = { value = 120,  rarity = "Godly" },
	["mortal"]             = { value = 120,  rarity = "Godly" },
	["candy"]              = { value = 95,   rarity = "Godly" },
	["pearlshine"]         = { value = 80,   rarity = "Godly" },
	["pearl"]              = { value = 75,   rarity = "Godly" },
	["beachy"]             = { value = 65,   rarity = "Godly" },
	["sands"]              = { value = 65,   rarity = "Godly" },
	["heartblade"]         = { value = 65,   rarity = "Godly" },
	["ornament"]           = { value = 65,   rarity = "Godly" },
	["red luger"]          = { value = 35,   rarity = "Godly" },
	["darkbringer"]        = { value = 32,   rarity = "Godly" },
	["elderwood revolver"] = { value = 32,   rarity = "Godly" },
	["iceblaster"]         = { value = 32,   rarity = "Godly" },
	["lightbringer"]       = { value = 32,   rarity = "Godly" },
	["phantom"]            = { value = 32,   rarity = "Godly" },
	["spectre"]            = { value = 32,   rarity = "Godly" },
	["candleflame"]        = { value = 30,   rarity = "Godly" },
	["elderwood blade"]    = { value = 30,   rarity = "Godly" },
	["makeshift"]          = { value = 30,   rarity = "Godly" },
	["sugar"]              = { value = 30,   rarity = "Godly" },
	["green luger"]        = { value = 23,   rarity = "Godly" },
	["amerilaser"]         = { value = 22,   rarity = "Godly" },
	["laser"]              = { value = 22,   rarity = "Godly" },
	["hallowgun"]          = { value = 20,   rarity = "Godly" },
	["luger"]              = { value = 20,   rarity = "Godly" },
	["nightblade"]         = { value = 20,   rarity = "Godly" },
	["shark"]              = { value = 20,   rarity = "Godly" },
	["icebeam"]            = { value = 18,   rarity = "Godly" },
	["plasmabeam"]         = { value = 18,   rarity = "Godly" },
	["swirly gun"]         = { value = 18,   rarity = "Godly" },
	["battleaxe ii"]       = { value = 17,   rarity = "Godly" },
	["blaster"]            = { value = 17,   rarity = "Godly" },
	["ginger luger"]       = { value = 17,   rarity = "Godly" },
	["pixel"]              = { value = 17,   rarity = "Godly" },
	["gemstone"]           = { value = 15,   rarity = "Godly" },
	["iceflake"]           = { value = 15,   rarity = "Godly" },
	["old glory"]          = { value = 15,   rarity = "Godly" },
	["plasmablade"]        = { value = 15,   rarity = "Godly" },
	["slasher"]            = { value = 15,   rarity = "Godly" },
	["vampire's edge"]     = { value = 15,   rarity = "Godly" },
	["deathshard"]         = { value = 13,   rarity = "Godly" },
	["eternalcane"]        = { value = 13,   rarity = "Godly" },
	["gingerblade"]        = { value = 13,   rarity = "Godly" },
	["jinglegun"]          = { value = 13,   rarity = "Godly" },
	["lugercane"]          = { value = 13,   rarity = "Godly" },
	["minty"]              = { value = 13,   rarity = "Godly" },
	["nebula"]             = { value = 13,   rarity = "Godly" },
	["virtual"]            = { value = 13,   rarity = "Godly" },
	["battleaxe"]          = { value = 12,   rarity = "Godly" },
	["cookiecane"]         = { value = 12,   rarity = "Godly" },
	["gingermint"]         = { value = 12,   rarity = "Godly" },
	["swirly blade"]       = { value = 12,   rarity = "Godly" },
	["chill"]              = { value = 10,   rarity = "Godly" },
	["clockwork"]          = { value = 10,   rarity = "Godly" },
	["fang"]               = { value = 10,   rarity = "Godly" },
	["frostsaber"]         = { value = 10,   rarity = "Godly" },
	["heat"]               = { value = 10,   rarity = "Godly" },
	["spider"]             = { value = 10,   rarity = "Godly" },
	["tides"]              = { value = 10,   rarity = "Godly" },
	["bioblade"]           = { value = 8,    rarity = "Godly" },
	["eternal iii"]        = { value = 8,    rarity = "Godly" },
	["eternal iv"]         = { value = 8,    rarity = "Godly" },
	["hallow's blade"]     = { value = 8,    rarity = "Godly" },
	["hallow's edge"]      = { value = 8,    rarity = "Godly" },
	["handsaw"]            = { value = 8,    rarity = "Godly" },
	["boneblade"]          = { value = 7,    rarity = "Godly" },
	["eternal"]            = { value = 7,    rarity = "Godly" },
	["eternal ii"]         = { value = 7,    rarity = "Godly" },
	["frostbite"]          = { value = 7,    rarity = "Godly" },
	["ghostblade"]         = { value = 7,    rarity = "Godly" },
	["ice dragon"]         = { value = 7,    rarity = "Godly" },
	["ice shard"]          = { value = 7,    rarity = "Godly" },
	["prismatic"]          = { value = 7,    rarity = "Godly" },
	["pumpking"]           = { value = 7,    rarity = "Godly" },
	["saw"]                = { value = 7,    rarity = "Godly" },
	["xmas"]               = { value = 7,    rarity = "Godly" },
	["eggblade"]           = { value = 5,    rarity = "Godly" },
	["flames"]             = { value = 5,    rarity = "Godly" },
	["snowflake"]          = { value = 5,    rarity = "Godly" },
	["winter's edge"]      = { value = 5,    rarity = "Godly" },
	["peppermint"]         = { value = 4,    rarity = "Godly" },
	["cookieblade"]        = { value = 3,    rarity = "Godly" },
	["blue seer"]          = { value = 3,    rarity = "Godly" },
	["purple seer"]        = { value = 3,    rarity = "Godly" },
	["red seer"]           = { value = 3,    rarity = "Godly" },
	["seer"]               = { value = 3,    rarity = "Godly" },
	["orange seer"]        = { value = 2,    rarity = "Godly" },
	["yellow seer"]        = { value = 2,    rarity = "Godly" },
-- ANCIENT
["gingerscope"]        = { value = 16000, rarity = "Ancient" },
["traveler's axe"]     = { value = 6600,  rarity = "Ancient" },
["celestial"]          = { value = 2075,  rarity = "Ancient" },
["vampire's axe"]      = { value = 1050,  rarity = "Ancient" },
["emberbow"]           = { value = 525,   rarity = "Ancient" },
["emberscythe"]        = { value = 525,   rarity = "Ancient" },
["harvester"]          = { value = 320,   rarity = "Ancient" },
["icepiercer"]         = { value = 230,   rarity = "Ancient" },
["icebreaker"]         = { value = 65,    rarity = "Ancient" },
["batwing"]            = { value = 40,    rarity = "Ancient" },
["elderwood scythe"]   = { value = 35,    rarity = "Ancient" },
["swirly axe"]         = { value = 35,    rarity = "Ancient" },
["hallowscythe"]       = { value = 30,    rarity = "Ancient" },
["logchopper"]         = { value = 17,    rarity = "Ancient" },
["icewing"]            = { value = 13,    rarity = "Ancient" },
-- VINTAGE
["blood"]              = { value = 8, rarity = "Vintage" },
["ghost"]              = { value = 8, rarity = "Vintage" },
["laser (vintage)"]    = { value = 8, rarity = "Vintage" },
["america"]            = { value = 7, rarity = "Vintage" },
["prince"]             = { value = 6, rarity = "Vintage" },
["shadow"]             = { value = 6, rarity = "Vintage" },
["phaser"]             = { value = 5, rarity = "Vintage" },
["cowboy"]             = { value = 4, rarity = "Vintage" },
["golden"]             = { value = 4, rarity = "Vintage" },
["splitter"]           = { value = 3, rarity = "Vintage" }
}
-- =====================================================================
-- БАЗА ХРОМ
-- =====================================================================
local CHROMA_DATABASE = {
["traveler's gun"] = 140000, ["evergun"] = 55000, ["evergreen"] = 44000,
["bauble"] = 31000, ["constellation"] = 27500, ["vampire's gun"] = 23500,
["alienbeam"] = 18500, ["raygun"] = 12500, ["emberscythe"] = 5250, ["sunrise"] = 8500,
["snowcannon"] = 7750, ["sunset"] = 5500, ["emberbow"] = 5000, ["blizzard"] = 5500,
["snowstorm"] = 4250, ["heart wand"] = 3500, ["watergun"] = 2300,
["snow dagger"] = 2350, ["icecream"] = 1525, ["ornament"] = 1825,
["treat"] = 1775, ["sweet"] = 1725, ["immortal"] = 1550, ["mortal"] = 1550,
["embersickle"] = 1650, ["sands"] = 850, ["beachy"] = 800,
["darkbringer"] = 65, ["lightbringer"] = 60, ["luger"] = 50,
["candleflame"] = 40, ["laser"] = 40, ["elderwood blade"] = 37,
["deathshard"] = 33, ["swirly gun"] = 35, ["cookiecane"] = 32,
["fang"] = 32, ["gemstone"] = 32, ["shark"] = 32, ["slasher"] = 30,
["heat"] = 28, ["seer"] = 27, ["gingerblade"] = 25, ["tides"] = 27,
["saw"] = 23, ["boneblade"] = 22, ["fire bat"] = 3, ["fire bear"] = 3,
["fire bunny"] = 3, ["fire cat"] = 3, ["fire dog"] = 3,
["fire fox"] = 3, ["fire pig"] = 3
}
-- =====================================================================
-- БАЗА ПЕТІВ
-- =====================================================================
local PET_DATABASE = {
["zombie dog"]           = { value = 750, rarity = "Common" },
["elf (2019)"]           = { value = 625, rarity = "Uncommon" },
["blue pumpkin (2018)"]  = { value = 220, rarity = "Common" },
["dogey"]                = { value = 150, rarity = "Uncommon" },
["red pumpkin (2018)"]   = { value = 120, rarity = "Common" },
["black cat"]            = { value = 75,  rarity = "Common" },
["green pumpkin (2018)"] = { value = 60,  rarity = "Common" },
["pumpkin (2017)"]       = { value = 55,  rarity = "Common" },
["mr. reindeer"]         = { value = 55,  rarity = "Rare" },
["piggy"]                = { value = 55,  rarity = "Common" },
["elf (2016)"]           = { value = 25,  rarity = "Common" },
["red pumpkin (2020)"]   = { value = 20,  rarity = "Legendary" },
["mr. snowman"]          = { value = 20,  rarity = "Common" },
["red pumpkin (2021)"]   = { value = 17,  rarity = "Legendary" },
["skully"]               = { value = 15,  rarity = "Legendary" },
["green pumpkin (2020)"] = { value = 12,  rarity = "Rare" },
["green pumpkin (2021)"] = { value = 10,  rarity = "Rare" },
["blue pumpkin (2020)"]  = { value = 7,   rarity = "Uncommon" },
["heart pet"]            = { value = 7,   rarity = "Common" },
["shadow pumpkin"]       = { value = 5,   rarity = "Uncommon" },
["nobledragon"]          = { value = 5,   rarity = "Legendary" },
["snowbear"]             = { value = 3,   rarity = "Common" },
["mechbug"]              = { value = 3,   rarity = "Rare" },
["ufo"]                  = { value = 3,   rarity = "Common" },
["chilly"]               = { value = 3,   rarity = "Uncommon" },
["eyeball"]              = { value = 3,   rarity = "Rare" },
["fairy"]                = { value = 3,   rarity = "Uncommon" },
["green pumpkin (2019)"] = { value = 3,   rarity = "Rare" },
["jetstream"]            = { value = 3,   rarity = "Rare" },
["overseer eye"]         = { value = 3,   rarity = "Legendary" },
["pengy"]                = { value = 3,   rarity = "Common" },
["purple pumpkin (2018)"]= { value = 3,   rarity = "Common" },
["red pumpkin (2019)"]   = { value = 3,   rarity = "Rare" },
["reindeer"]             = { value = 3,   rarity = "Common" },
["rudolph"]              = { value = 3,   rarity = "Legendary" },
["seahorsey"]            = { value = 3,   rarity = "Uncommon" },
["tankie"]               = { value = 3,   rarity = "Rare" },
["vampire bat"]          = { value = 3,   rarity = "Legendary" },
["lil' alien"]           = { value = 1,   rarity = "Common" },
["badger"]               = { value = 1,   rarity = "Common" },
["blue pumpkin (2019)"]  = { value = 1,   rarity = "Uncommon" }
}
-- =====================================================================
-- ХЕЛПЕРИ
-- =====================================================================
local function normalize(s)
if not s then return "" end
s = s:lower()
s = s:gsub("^%s+", ""):gsub("%s+$", "")
s = s:gsub("%s+", " ")
return s
end
local function stripRichText(text)
if not text then return text end
return (text:gsub("<[^>]->", ""))
end
local function isGuiActuallyVisible(obj)
if not obj then return false end
if obj:IsA("GuiObject") then
if not obj.Visible then return false end
if obj.AbsoluteSize.X <= 0 or obj.AbsoluteSize.Y <= 0 then
return false
end
end
local parent = obj.Parent
while parent do
if parent:IsA("GuiObject") then
if not parent.Visible then return false end
if parent.AbsoluteSize.X <= 0
or parent.AbsoluteSize.Y <= 0 then
return false
end
end
parent = parent.Parent
end
if obj:IsA("ImageLabel") or obj:IsA("ImageButton") then
if obj.ImageTransparency >= 1 then return false end
end
if obj:IsA("TextLabel") or obj:IsA("TextButton") then
if obj.TextTransparency >= 1 then return false end
end
return true
end
local function isChromaVisible(slot)
if not slot then return false end
local tags = slot:FindFirstChild("Tags", true)
if not tags then return false end
local chroma = tags:FindFirstChild("Chroma", true)
if not chroma then return false end
if not isGuiActuallyVisible(chroma) then return false end
return true
end
local function getItemNameAndColor(itemName)
if not itemName then return nil, nil end
if (itemName:IsA("TextLabel") or itemName:IsA("TextButton"))
and isGuiActuallyVisible(itemName)
and itemName.Text ~= "" then
return stripRichText(itemName.Text), itemName.TextColor3
end
for _, obj in ipairs(itemName:GetDescendants()) do
if obj:IsA("TextLabel") or obj:IsA("TextButton") then
if isGuiActuallyVisible(obj) and obj.Text ~= "" then
return stripRichText(obj.Text), obj.TextColor3
end
end
end
return nil, nil
end
local function parseAmount(text)
if not text then return 1 end
local num = tonumber(tostring(text):match("%d+"))
return num or 1
end
local function stripChromaPrefix(name)
local k = normalize(name)
k = k:gsub("^chroma%s+", "")
k = k:gsub("^c%.%s*", "")
k = k:gsub("^c%s+", "")
k = k:gsub("%s*%b()", "")
k = k:gsub("^%s+", ""):gsub("%s+$", "")
return k
end
-- =====================================================================
-- ЧИТАННЯ ТРЕЙДУ
-- =====================================================================
local function getContainer(side)
local gui = PlayerGui:FindFirstChild("TradeGUI")
local main = gui and gui:FindFirstChild("Container")
local trade = main and main:FindFirstChild("Trade")
local offer = trade and trade:FindFirstChild(side)
return offer and offer:FindFirstChild("Container")
end
local function getWeapon(slot)
if not slot then return "NON", "1", 0 end
if slot:IsA("GuiObject") and not slot.Visible then return "NON", "1", 0 end
local itemName = slot:FindFirstChild("ItemName", true)
if not itemName then return "NON", "1", 0 end
if itemName:IsA("GuiObject") and not itemName.Visible then return "NON", "1", 0 end
local name, color = getItemNameAndColor(itemName)
if not name or name == "" then return "NON", "1", 0 end
local nameLower = name:lower()
local chromaVisible = isChromaVisible(slot)
local nameSaysChroma = nameLower:find("chroma", 1, true) ~= nil
or nameLower:find("^c%.", 1) ~= nil
or nameLower:find("^c%s", 1) ~= nil
local innerContainer = slot:FindFirstChild("Container")
local amount = innerContainer and innerContainer:FindFirstChild("Amount")
local amountText = "1"
if amount then
if amount:IsA("TextLabel") or amount:IsA("TextButton") then
if amount.Text ~= "" then amountText = amount.Text end
else
for _, obj in ipairs(amount:GetDescendants()) do
if obj:IsA("TextLabel") or obj:IsA("TextButton") then
if obj.Visible and obj.Text ~= "" then
amountText = obj.Text
break
end
end
end
end
end
-- 1) CHROMA
if chromaVisible then
local base = stripChromaPrefix(name)
local v = CHROMA_DATABASE[base]
if not v then return "NON", "1", 0 end
return name, amountText, v
end
if nameSaysChroma then
return "NON", "1", 0
end
-- 2) КОЛІР
local rarity = colorToRarity(color)
if DEBUG then
print(string.format("[ITEM] %s | color=%s | rarity=%s",
name, tostring(color), tostring(rarity)))
end
-- ⛔ ЗЕЛЕНИЙ (Rare) — завжди ігноруємо, незалежно від бази
if rarity == "Rare" then
if DEBUG then print("[IGNORE] Rare color:", name) end
return "NON", "1", 0
end
-- 3) ІНШІ RARITY — беремо з бази
local key = normalize(name)
local entry = ITEM_DATABASE[key]
if not entry then
local noParen = key:gsub("%s*%b()", ""):gsub("^%s+", ""):gsub("%s+$", "")
entry = ITEM_DATABASE[noParen]
end
if entry then
if rarity and entry.rarity ~= rarity then
if DEBUG then
print(string.format("[MISMATCH] %s: DB=%s, shown=%s → NON",
name, entry.rarity, rarity))
end
return "NON", "1", 0
end
return name, amountText, entry.value
end
-- ПЕТИ
local petEntry = PET_DATABASE[key]
if not petEntry then
local noParen = key:gsub("%s*%b()", ""):gsub("^%s+", ""):gsub("%s+$", "")
petEntry = PET_DATABASE[noParen]
end
if petEntry then
if petEntry.rarity == "Rare" then
return "NON", "1", 0
end
if rarity and petEntry.rarity ~= rarity then
return "NON", "1", 0
end
return name, amountText, petEntry.value
end
return "NON", "1", 0
end
local function getOfferTotal(side)
local container = getContainer(side)
local total = 0
for i = 1, 4 do
local slot = container and container:FindFirstChild("NewItem" .. i)
local _, amountText, value = getWeapon(slot)
if value and value > 0 then
local amount = parseAmount(amountText)
total = total + value * amount
end
end
return total
end
-- =====================================================================
-- GUI
-- =====================================================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TradeValueChecker"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.DisplayOrder = 2147483647
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = PlayerGui
task.spawn(function()
while screenGui.Parent do
screenGui.DisplayOrder = 2147483647
task.wait(0.5)
end
end)
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 340, 0, 210)
frame.Position = UDim2.new(0, 20, 0.5, -105)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 28)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.ZIndex = 1000
frame.Parent = screenGui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 32)
title.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
title.BorderSizePixel = 0
title.Text = "Trade Value Checker"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 15
title.ZIndex = 1001
title.Parent = frame
Instance.new("UICorner", title).CornerRadius = UDim.new(0, 10)
local theirLabel = Instance.new("TextLabel")
theirLabel.Size = UDim2.new(1, -20, 0, 24)
theirLabel.Position = UDim2.new(0, 10, 0, 42)
theirLabel.BackgroundTransparency = 1
theirLabel.Text = "Their offer: 0"
theirLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
theirLabel.Font = Enum.Font.Gotham
theirLabel.TextSize = 14
theirLabel.TextXAlignment = Enum.TextXAlignment.Left
theirLabel.ZIndex = 1001
theirLabel.Parent = frame
local yourLabel = Instance.new("TextLabel")
yourLabel.Size = UDim2.new(1, -20, 0, 24)
yourLabel.Position = UDim2.new(0, 10, 0, 70)
yourLabel.BackgroundTransparency = 1
yourLabel.Text = "Your offer: 0"
yourLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
yourLabel.Font = Enum.Font.Gotham
yourLabel.TextSize = 14
yourLabel.TextXAlignment = Enum.TextXAlignment.Left
yourLabel.ZIndex = 1001
yourLabel.Parent = frame
local resultLabel = Instance.new("TextLabel")
resultLabel.Size = UDim2.new(1, -20, 0, 46)
resultLabel.Position = UDim2.new(0, 10, 0, 100)
resultLabel.BackgroundTransparency = 1
resultLabel.Text = "Waiting..."
resultLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
resultLabel.Font = Enum.Font.GothamBold
resultLabel.TextSize = 22
resultLabel.TextXAlignment = Enum.TextXAlignment.Left
resultLabel.ZIndex = 1002
resultLabel.Parent = frame
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -20, 0, 40)
statusLabel.Position = UDim2.new(0, 10, 0, 155)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = ""
statusLabel.TextColor3 = Color3.fromRGB(170, 170, 170)
statusLabel.Font = Enum.Font.Gotham
statusLabel.TextSize = 12
statusLabel.TextWrapped = true
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.ZIndex = 1001
statusLabel.Parent = frame
-- =====================================================================
-- ЦИКЛ
-- =====================================================================
while true do
local theirTotal = getOfferTotal("TheirOffer")
local yourTotal = getOfferTotal("YourOffer")
theirLabel.Text = string.format("Their offer: %d", theirTotal)
yourLabel.Text  = string.format("Your offer: %d", yourTotal)
local diff = theirTotal - yourTotal
if theirTotal == 0 and yourTotal == 0 then
resultLabel.Text = "НЕ ТРЕЙДИТСЯ НА НОВИЕ ГОДЛИ!"
resultLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
statusLabel.Text = "НЕ ТРЕЙДИТСЯ НА НОВИЕ ГОДЛИ!"
elseif diff > 0 then
resultLabel.Text = string.format("+%d  (В Плюсе👍)", diff)
resultLabel.TextColor3 = Color3.fromRGB(60, 220, 90)
statusLabel.Text = "Ты получаеш больше чем Ты даеш!"
elseif diff == 0 then
resultLabel.Text = "0  (Ровноценно)"
resultLabel.TextColor3 = Color3.fromRGB(255, 165, 0)
statusLabel.Text = "Ровно ценный трейд"
else
resultLabel.Text = string.format("%d  (В Минусе)", diff)
resultLabel.TextColor3 = Color3.fromRGB(235, 65, 65)
statusLabel.Text = "Ты отдаеш больше чем получаеш😭"
end
task.wait(0.4)
end
