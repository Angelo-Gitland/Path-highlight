local Players       = game:GetService("Players")
local TweenService  = game:GetService("TweenService")
local localPlayer   = Players.LocalPlayer
local ScreenGui     = Instance.new("ScreenGui")
local MainFrame     = Instance.new("Frame")
local TitleBar      = Instance.new("Frame")
local Title         = Instance.new("TextLabel")
local MinimizeBtn   = Instance.new("TextButton")
local CloseBtn      = Instance.new("TextButton")
local StatusLabel   = Instance.new("TextLabel")
local ESPButton     = Instance.new("TextButton")
local CreditsButton = Instance.new("TextButton")

local espEnabled      = false
local originalColors  = {}
local wrongPaths      = {}
local espHighlights   = {}
local segments        = {}

-- … [insert your GUI‐setup code here] …

for _, part in ipairs(workspace:GetDescendants()) do
    if part:IsA("BasePart") and part.Parent.Parent.Name:match("Segment") then
        segments[#segments + 1] = part
        originalColors[part] = part.Color
        if part.CanCollide == false then
            wrongPaths[part] = true
        end
    end
end

local function toggleESP()
    espEnabled = not espEnabled
    StatusLabel.Text = espEnabled and "Status: On" or "Status: Off"

    if espEnabled then
        for _, part in ipairs(segments) do
            local isWrong = wrongPaths[part]
            local color   = isWrong and Color3.fromRGB(255,  0,   0)
                              or Color3.fromRGB(  0, 255,   0)

            -- recolor part (optional)
            part.Color = color

            -- create or update Highlight
            if not espHighlights[part] then
                local hl = Instance.new("Highlight")
                hl.Adornee            = part
                hl.FillTransparency   = 0.8
                hl.OutlineTransparency= 0
                hl.FillColor          = color
                hl.OutlineColor       = color
                hl.Parent             = part
                espHighlights[part]   = hl
            else
                espHighlights[part].OutlineColor = color
                espHighlights[part].FillColor    = color
            end
        end
    else
        for _, part in ipairs(segments) do
            -- restore original color
            part.Color = originalColors[part] or part.Color

            -- destroy highlight
            if espHighlights[part] then
                espHighlights[part]:Destroy()
                espHighlights[part] = nil
            end
        end
    end
end

ESPButton.MouseButton1Click:Connect(toggleESP)

toggleESP()
