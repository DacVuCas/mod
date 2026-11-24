# Modern Mod (SoWatDiKhapVN) – port of v1.6 from Fabric 1.16.5 to Minecraft 26.3

This is a **source project**. It was written without access to the Minecraft 26.3 / Fabric files
(the sandbox it was made in could not download them), so **it has never been compiled or launched**.
Expect to fix a few compile errors (see "Most likely trouble spots"). Everything that came out of the
original jar was preserved: 43 blocks, 3 items, 2 creative tabs, 2 container GUIs, all models/textures.

## Build (recommended way)
1. Install **JDK 25** (26.x needs Java 25).
2. Go to <https://fabricmc.net/develop>, pick Minecraft **26.3**, and generate a template project
   (mod id `mainmodfabric`). Copy the `build.gradle`, `settings.gradle`, `gradle.properties` and the
   Gradle wrapper (`gradlew`, `gradle/`) it gives you over the ones here — those have the right
   Loom / Fabric API versions. (The ones in this folder are best guesses.)
3. In the project folder run `./gradlew build` (Windows: `gradlew.bat build`).
4. The mod is `build/libs/Modern_Mod-1.6.0+26.3.jar` (not the `-sources` one). Put it in `mods/` together with Fabric API.

## What changed vs. the 1.16.5 jar
* 41 near-identical block classes -> `DecoBlock` (+ per-direction hitboxes from the original code);
  the two inventory blocks -> `ContainerDecoBlock` + `BinBlockEntity` / `TvStandBlockEntity`.
* Same ids (`mainmodfabric:*`), strength 1.0/10.0, no-occlusion, stone sound, cutout render layer.
* Container GUIs keep the original slot positions (36 slots / 2x24 slots) and textures.
* Resources: `textures/blocks|items` -> `textures/block|item`; model JSON cleaned (Blockbench-only keys removed);
  new `assets/mainmodfabric/items/*.json` item definitions (required since 1.21.4);
  new loot tables so blocks drop themselves; `pack.mcmeta` removed (Fabric supplies it).
* 13 models had faces pointing at a texture that never existed; they now use `block/black`.
* Dropped: comparator output of the two inventory blocks, "sided" hopper access (hoppers still work).
* Worlds from 1.16.5 must first be opened in vanilla (upgrade) – block ids are unchanged so the blocks should carry over.

## Most likely trouble spots (if `./gradlew build` complains)
* `ClientInit`: `BlockRenderLayerMap.putBlock(...)` / `ChunkSectionLayer.CUTOUT` – check the Fabric API 26.x docs for the current form.
* `DecoContainerScreen`: `GuiGraphicsExtractor` import, `extractBackground`, `blit(...)` argument list.
* `DecoContainerBlockEntity`: `loadAdditional/saveAdditional` (`ValueInput/ValueOutput`) and `RandomizableContainerBlockEntity` abstract methods.
* `DecoBlock`: `rotate/mirror/propagatesSkylightDown` signatures.
* `data/.../loot_table/blocks` folder name (singular since 1.21).
* If blocks look black/transparent, or GUI text is misplaced, tell me what you see.

## Build on GitHub (no local setup)
1. Create a GitHub repo and upload everything in this folder (keep the `.github` folder).
2. Open the repo's **Actions** tab -> "Build mod jar" run -> download the `Modern_Mod_26.3_jar` artifact (a zip containing the .jar).
3. If it fails, open the failed step's log. The versions in `gradle.properties` (loom_version, fabric_api_version) are guesses - fix them from https://fabricmc.net/develop first.
