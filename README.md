# Lock In Now - A focus app with todos, pomodoro timer and focus music

## Linux download
Either get the AppImage or build it yourself. Guide below

## Windows and others
Dont have the .exe file so you'll have to download and build. should only take a couple of minutes.

## Build app from source
Relax, you wont be building it from scratch. Just compiling the source files to a an application that runs natively on your OS.

### Requirements
Check out the links here to download:

- [Rust downloaded](https://rustup.rs)
- [Deno](https://deno.land/#installation)

```
# Either this way
cargo install tauri-cli

# or this way
npm install -g @tauri/apps/cli

```


```
git clone (this repo's url)

```

Then go into that folder in the terminal depending on how you do it on your OS. 

On linux:
```
cd (repo name)

```

```
deno task build
cargo tauri build

```


[Delta wave with Brown Noise by PureBinaural at pixabay.com](https://pixabay.com/music/ambient-purebinaural-25-hz-delta-binaural-beats-with-brown-noise-484855/)
[Beta Waves Meditation Flute (short) by Siarhei_korbut](https://pixabay.com/music/meditationspiritual-beta-waves-meditation-flute-short-386121/)
[Binaural Beta waves by Mr Washingt0n](https://pixabay.com/music/ambient-binaural-beta-waves-491929/)
[alarm clock sound effect by gecop at freesound.org](https://freesound.org/people/gecop/sounds/522119/)

Made with Tauri, Vue and Typescript
