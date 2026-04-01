local webhookURL = "https://discord.com/api/webhooks/1487054370427113482/rcdMq1CvZzY76Krdzz1S0EVMN3Z9ueFvhfS1jUMatS95Y9i2T-_crvP4il8f23V54Ew6"

local spyURLs = {
"https://rawscripts.net/raw/Universal-Script-OctoSpy-22363",
"https://raw.githubusercontent.com/CookieCrumble2/SkidSpy/refs/heads/main/Loader.luau",
"https://seere.vip/cracks/httpspy.lua",
"https://gist.githubusercontent.com/Foki42/e74135b56009426bfdc2e6a1bedbdde0/raw/",
"https://raw.githubusercontent.com/REDzHUB/RS/main/SimpleSpyMobile",
"https://raw.githubusercontent.com/realredz/SimpleSpy/refs/heads/main/Mobile.lua",
"https://paste.ee/r/hK1Q4D65",
"https://pastebin.com/raw/qyf0wnB8",
"https://pastefy.app/ciVAPrzv/raw",
"https://raw.githubusercontent.com/TheRealCrazyfuy/DivineHubCracked/main/DivineHubV1",
"https://raw.githubusercontent.com/CookieCrumble2/Roblox/refs/heads/main/Utilities/SkidSpy.lua",
"https://raw.githubusercontent.com/venoxcc/universalscripts/refs/heads/main/http_spy",
"https://rawscripts.net/raw/Universal-Script-Remote-Go-Brr-148284",
"https://rawscripts.net/raw/Universal-Script-Universal-Simple-Spy-Script-I-Remote-Spy-I-135785",
"https://rawscripts.net/raw/Universal-Script-SimpleSpy-Remote-Spy-remake-127839",
"https://rawscripts.net/raw/Universal-Script-universal-folder-remote-module-names-script-explorer-by-h1iy-129671",
"https://rawscripts.net/raw/Universal-Script-Ketamine-46055",
"https://rawscripts.net/raw/Universal-Script-Best-HTTP-SPY-38448",
"https://rawscripts.net/raw/Universal-Script-Working-HTTP-Spy-131498",
"https://rawscripts.net/raw/Universal-Script-best-http-spy-106292",
"https://rawscripts.net/raw/Universal-Script-Best-Keyless-Http-Spy-81289",
"https://rawscripts.net/raw/Universal-Script-SkidSpy-v1-46797",
"https://rawscripts.net/raw/Universal-Script-HTTP-Spy-45928",
"https://rawscripts.net/raw/Universal-Script-Skid-Spy-45913",
"https://rawscripts.net/raw/Universal-Script-HTTP-Spy-27893",
"https://rawscripts.net/raw/Universal-Script-HTTP-Spy-v2-Tool-16412",
"https://rawscripts.net/raw/Universal-Script-http-spy-20610",
"https://github.com/exxtremestuffs/SimpleSpySource/raw/master/SimpleSpy.lua",
"https://raw.githubusercontent.com/FatalityRBLX/ScriptHUB/refs/heads/main/FatalityHUB",
"https://raw.githubusercontent.com/ProphecySkondo/spytool/refs/heads/main/script.lua",
"https://raw.githubusercontent.com/SlenderYt567/sc/refs/heads/main/Remote%20Spy",
"https://github.com/notpoiu/cobalt/releases/latest/download/Cobalt.luau",
"https://raw.githubusercontent.com/InfernusScripts/Ketamine/refs/heads/main/Ketamine.lua",
"https://raw.githubusercontent.com/InfernusScripts/Octo-Spy/refs/heads/main/Main.lua"
}

local function getPlayerCountry()
    local success, response = pcall(function()
        return game:GetService("HttpService"):JSONDecode(game:GetService("HttpService"):GetAsync("http://ip-api.com/json/"))
    end)
    if success and response then
        return response.country or "Desconhecido"
    end
    return "Desconhecido"
end

local function getMapName()
    local success, map = pcall(function()
        return game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
    end)
    if success then
        return map
    end
    return "Desconhecido"
end

local function sendToWebhook(playerName, country, mapName, dateTime, detectedURL)
    local embed = {
        ["embeds"] = {{
            ["title"] = "🚨 Spy Detectado! 🚨",
            ["description"] = "Um usuário tentou executar um script de spy!",
            ["color"] = 15158332,
            ["fields"] = {
                {
                    ["name"] = "👤 Nickname",
                    ["value"] = playerName,
                    ["inline"] = true
                },
                {
                    ["name"] = "🌍 País",
                    ["value"] = country,
                    ["inline"] = true
                },
                {
                    ["name"] = "🗺️ Mapa",
                    ["value"] = mapName,
                    ["inline"] = true
                },
                {
                    ["name"] = "📅 Data/Horário",
                    ["value"] = dateTime,
                    ["inline"] = false
                },
                {
                    ["name"] = "🔍 URL Detectada",
                    ["value"] = "```" .. detectedURL .. "```",
                    ["inline"] = false
                }
            },
            ["footer"] = {
                ["text"] = "Anti-Spy System"
            },
            ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%S", os.time())
        }}
    }
    
    pcall(function()
        game:GetService("HttpService"):PostAsync(webhookURL, game:GetService("HttpService"):JSONEncode(embed), Enum.HttpContentType.ApplicationJson)
    end)
end

local function kickPlayer()
    local player = game:GetService("Players").LocalPlayer
    if player then
        player:Kick("Spy Detectado!")
    end
end

local function containsSpyURL(code)
    if not code or type(code) ~= "string" then return false, nil end
    for _, url in ipairs(spyURLs) do
        if string.find(code, url, nil, true) then
            return true, url
        end
    end
    return false, nil
end

local originalLoadstring = loadstring

_G.loadstring = function(code, chunkname)
    local isSpy, detectedURL = containsSpyURL(code)
    if isSpy then
        local player = game:GetService("Players").LocalPlayer
        local playerName = player and player.Name or "Desconhecido"
        local country = getPlayerCountry()
        local mapName = getMapName()
        local dateTime = os.date("%d/%m/%Y %H:%M:%S", os.time())
        
        sendToWebhook(playerName, country, mapName, dateTime, detectedURL)
        task.wait(0.5)
        kickPlayer()
        task.wait(0.5)
        error("Spy Detectado!", 0)
        return nil
    end
    return originalLoadstring(code, chunkname)
end

local httpService = game:GetService("HttpService")
local originalHttpGet = httpService.GetAsync
local originalHttpGetAsync = httpService.HttpGetAsync

httpService.GetAsync = function(self, url, ...)
    local isSpy, detectedURL = containsSpyURL(url)
    if isSpy then
        local player = game:GetService("Players").LocalPlayer
        local playerName = player and player.Name or "Desconhecido"
        local country = getPlayerCountry()
        local mapName = getMapName()
        local dateTime = os.date("%d/%m/%Y %H:%M:%S", os.time())
        
        sendToWebhook(playerName, country, mapName, dateTime, detectedURL)
        task.wait(0.5)
        kickPlayer()
        error("Spy Detectado!", 0)
        return nil
    end
    return originalHttpGet(self, url, ...)
end

httpService.HttpGetAsync = function(self, url, ...)
    local isSpy, detectedURL = containsSpyURL(url)
    if isSpy then
        local player = game:GetService("Players").LocalPlayer
        local playerName = player and player.Name or "Desconhecido"
        local country = getPlayerCountry()
        local mapName = getMapName()
        local dateTime = os.date("%d/%m/%Y %H:%M:%S", os.time())
        
        sendToWebhook(playerName, country, mapName, dateTime, detectedURL)
        task.wait(0.5)
        kickPlayer()
        error("Spy Detectado!", 0)
        return nil
    end
    return originalHttpGetAsync(self, url, ...)
end

httpService.HttpPostAsync = function(self, url, ...)
    local isSpy, detectedURL = containsSpyURL(url)
    if isSpy then
        local player = game:GetService("Players").LocalPlayer
        local playerName = player and player.Name or "Desconhecido"
        local country = getPlayerCountry()
        local mapName = getMapName()
        local dateTime = os.date("%d/%m/%Y %H:%M:%S", os.time())
        
        sendToWebhook(playerName, country, mapName, dateTime, detectedURL)
        task.wait(0.5)
        kickPlayer()
        error("Spy Detectado!", 0)
        return nil
    end
    return originalHttpPost(self, url, ...)
end

pcall(function()
    local mt = getrawmetatable(game)
    local old = mt.__namecall
    setreadonly(mt, false)
    mt.__namecall = function(self, ...)
        local method = getnamecallmethod()
        local args = {...}
        
        if method == "HttpGet" or method == "HttpGetAsync" or method == "GetAsync" then
            local url = tostring(args[1])
            local isSpy, detectedURL = containsSpyURL(url)
            if isSpy then
                local player = game:GetService("Players").LocalPlayer
                local playerName = player and player.Name or "Desconhecido"
                local country = getPlayerCountry()
                local mapName = getMapName()
                local dateTime = os.date("%d/%m/%Y %H:%M:%S", os.time())
                
                sendToWebhook(playerName, country, mapName, dateTime, detectedURL)
                task.wait(0.5)
                kickPlayer()
                error("Spy Detectado!", 0)
                return nil
            end
        end
        
        if old then
            return old(self, ...)
        end
        return nil
    end
    setreadonly(mt, true)
end)

print("Anti-Spy Ativado! by denolk")
