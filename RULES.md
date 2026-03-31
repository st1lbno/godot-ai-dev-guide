# Godot AI Development Rules

Core rules for AI-assisted Godot game development. Follow these unless a project's own instructions override them.

---

## Project Setup

- Use **Godot 4.5+** standard edition (NOT .NET). HTML5 export is unstable in .NET builds.
- Install the **godot-mcp-bridge** plugin ([github.com/st1lbno/godot-mcp-bridge](https://github.com/st1lbno/godot-mcp-bridge)) for AI visibility into the running game.
- The MCP bridge **auto-selects an available port** from 9001-9020. No manual port configuration needed.
- Multiple Godot games can run simultaneously — each gets its own port automatically.
- Port files are written to `<project>/.godot/mcp_port` and `/tmp/godot_mcp_<name>.port` for discovery.
- Add the MCP server to Claude Code global settings (`~/.claude/settings.json`).
- For pixel art projects, set `default_texture_filter=0` (Nearest) in Project Settings to avoid blurry sprites.

---

## Development Workflow

- **NEVER open the Godot editor** — all development happens through code files and CLI.
- Launch the game with:
  ```
  /Applications/Godot.app/Contents/MacOS/Godot --path <project_dir>
  ```
- Use `--headless` flag for non-visual testing (bot runs, CI).
- Use `--headless --import` to force reimport and check for compile errors before launching.
- Use `--headless --quit` to quick-check for script errors without running the game.
- After adding new scripts with `class_name`, run `--headless --import` to register them before other scripts can reference the new classes.

---

## Testing

- **Build an autoplay bot as early as possible** — it is your primary testing tool.
- The bot should read game state directly (not via WebSocket) and execute actions through the same code paths as the player.
- Bot strategies:
  - **RandomBot** — baseline, catches crashes and edge cases
  - **GreedyBot** — aggressive, tests offensive mechanics
  - **SurvivalBot** — defensive, tests healing/blocking/escape paths
- Bot should log results to `user://bot_results.jsonl` for analysis.
- Bot should auto-restart after game over (persist state via temp file).
- Toggle bot with a keyboard shortcut (**B key** convention).
- Run headless batch tests: N games, analyze win rate, average turns, common death causes.

---

## MCP Bridge Usage

- Use **take_screenshot** to see the game state visually.
- Use **get_game_state** / **get_grid_state** for programmatic state inspection.
- Use **execute_gdscript** to run arbitrary code in the live game (toggle bot, inspect nodes, change settings).
- Use **simulate_click** / **simulate_key** for input injection.
- The MCP server auto-discovers the Godot port (env var > `.godot/mcp_port` > `/tmp/godot_mcp_*.port` scan > fallback 9001).
- Use **discover** to list all running Godot MCP instances and **connect_to** to switch between them.
- `NODE_PATH=$(npm root -g)` is needed for the `ws` module when using manual node commands.
- Screenshots are saved to `/tmp/` (configurable) — use the Read tool to view them.

---

## Art Pipeline

- Generate sprites using Gemini (or similar) with **SOLID BLACK backgrounds** — NOT pink, NOT transparent, NOT checkerboard.
- Black background + JPEG compression = dark fringe that is invisible against dark game backgrounds.
- Process sprites with `scripts/tools/process_sprites.py`: splits sheets, removes background, autocrops.
- Store generation prompts in `docs/prompts/` for reproducibility.
- Store raw generated images in `docs/concepts/`.
- Store processed game-ready sprites in `assets/sprites/`.
- Sprite scale: `ENTITY_SCALE` constant in the battle scene controls size relative to tiles.

---

## Common Gotchas

- **GDScript type inference:** Use explicit types (`var x: Type = ...`) for variables assigned from Dictionary lookups or method returns that Godot cannot infer.
- **class_name registration** requires `--import` before other scripts can reference new classes.
- **WebSocket screenshot** payloads can be large — save to disk instead of sending base64 over the wire.
- **Godot 4.5 treats some warnings as errors** — always fix type inference warnings.
- **Bot autoplay_enabled flag** does not survive scene reloads — persist to a temp file.

---

## Integration with Anima Publisher OS

### Analytics
- **Firebase / GA4** for events, **Sentry** for crash reporting.

### Monetization
- **AdMob** (mobile), **CrazyGames SDK** (web), **IAP** per platform.

### Remote Config
- **Firebase Remote Config** for live tuning.

### CI/CD
- **GitHub Actions** for lint, headless bot tests, multi-platform export.

### Standard Events
All games should track these events:
- `session_start`
- `level_start`
- `level_complete`
- `level_fail`
