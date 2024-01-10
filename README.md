# openmusic-compatible server specifications
An *openmusic-compatible* server is any public server which [the openmusic app](https://openmusic.app) may communicate with to serve music metadata and audio. A server may be abstract and created with your preferred language/library of choice, with your own algorithms and caching; so long as each endpoint conforms to the Return Types defined below, it is *openmusic-compatible*.

### Copyright Notice
The content provided by your server MUST be owned by you, the content deliverer. It is the responsibility of the content deliverer to ensure that copyright laws for your country are followed, and to ensure the legal safety of openmusic users. Failure to do so may result in server termination by your ISP, and legal pursuit by the true owners of the content you are delivering.


# Return Types
The following define the return objects that each endpoint conforms to, in pseudocode. \<type\>? denotes an optional (may contain nil), \<type\>[] denotes an array containing that type.

### FetchedTrack
```
FetchedTrack -> {
    TrackID: String
    Title: String
    Playback_Clean: Int?
    Playback_Explicit: Int?
    Length: Int
    Index: Int
    Views: Int
    Album: SearchedAlbum
    Features: SearchedArtist[]
}
```
### ImportedTrack
```
ImportedTrack -> {
    TrackID: String
    Title: String
    Playback_Clean: Int?
    Playback_Explicit: Int?
    Length: Int
    Index: Int
    Views: Int
    Album: SearchedAlbum
    Features: SearchedArtist[]
    titleScore: Float
    albumScore: Float
    artistScore: Float
}
```
`titleScore`, `albumScore`, and `artistScore` must be normalized, and denote the fuzzy text similarity to the track specified by the `/exact` endpoint.
### PlaylistTrack
```
PlaylistTrack -> {
    title: String
    album: String
    artists: String
}
```
### SearchedAlbum
```
SearchedAlbum -> {
    AlbumID: String
    Title: String
    Artwork: String
    AlbumType: String
    Year: Int
    Artists: SearchedArtist[]
}
```
### FetchedAlbum
```
FetchedAlbum -> {
    AlbumID: String
    Title: String
    Artwork: String
    AlbumType: String
    Year: Int
    Artists: SearchedArtist[]
    Tracks: FetchedTrack[]
    Features: SearchedArtist[]
}
```
### SearchedArtist
```
SearchedArtist -> {
    ArtistID: String
    Name: String
    Profile_Photo: String
    Subscribers: Int
}
```
### FetchedArtist
```
FetchedArtist -> {
    ArtistID: String
    Name: String
    Profile_Photo: String
    Subscribers: Int
    Albums: SearchedAlbum[]
    Singles: SearchedAlbum[]
    Tracks: FetchedTrack[]
}
```
### FetchedPlayback
```
FetchedPlayback -> {
    PlaybackID: Int
    YT_Video_ID: String
    YT_Audio_ID: String
    Playback_Audio_URL: String
}
```
### ExploreShelf
```
ExploreShelf -> {
    Title: String
    Albums: SearchedAlbum[]
}
```

# Server Endpoints
An endpoint may return a single Return Type as a dictionary, or a dictionary containing multiple objects and arrays, as specified.

### `/status`
Returns the status of the server. Hopefully you set `online=True`.

`om_verify` is the verification code used to denote your server as "**Verified**" in the openmusic app. If you do not run a trusted server, leave `om_verify` blank and the openmusic app will denote your server as "**Online**".
```
/status -> {
    online: Bool
    om_verify: String
    }
```
### `/search?q=<query>`
Returns search results for `<query>`
```
/search?q=<query> -> {
    Tracks: FetchedTrack[]
    Albums: SearchedAlbum[]
    Singles: SearchedAlbum[]
    Artists: SearchedArtist[]
}
```
### `/album?id=<AlbumID>`
Returns the album with `FetchedAlbum.AlbumID=<AlbumID>`
```
/album?id=<AlbumID> -> FetchedAlbum
```
### `/artist?id=<ArtistID>`
Returns the artist with `FetchedArtist.ArtistID=<ArtistID>`
```
/artist?id=<ArtistID> -> FetchedArtist
```
### `/playback?id=<PlaybackID>`
Returns the playback with `FetchedPlayback.PlaybackID=<PlaybackID>`
```
/playback?id=<PlaybackID> -> FetchedPlayback
```
### `/exact?song=<song>&album=<album>&artist=<artist>`
Returns tracks that fuzzy match `<song>`, `<album>`, and `<artist>`
* `<song>`: Track title
* `<album>`: Album title
* `<artists>`: Artist names, comma seperated
```
/exact?song=<title>&album=<album title>&artist=<artist name> -> {
    Tracks: ImportedTrack[]
}
```
### `/playlistinfo?platform=<platform>&id=<id>`
Returns metadata about a playlist from another platform. See the [PlaylistExtractor](https://github.com/OatsCG/PlaylistExtractor) for help.
* `<platform>`: One of `['apple', 'spotify']`
* `<id>`: The ID in the playlist's URL
```
/playlistinfo?platform=<platform>&id=<id> -> {
    name: String
    description: String
    artwork: String
    playlistID: String
}
```
### `/playlisttracks?platform=<platform>&id=<id>`
Returns the tracks in a playlist from another platform. See the [PlaylistExtractor](https://github.com/OatsCG/PlaylistExtractor) for help.
* `<platform>`: One of `['apple', 'spotify']`
* `<id>`: The ID in the playlist's URL
```
/playlisttracks?platform=<platform>&id=<id> -> {
    name: String
    description: String
    artwork: String
    playlistID: String
    tracks: PlaylistTrack[]
}
```
### `/explore`
Returns shelves to display in the Explore page
```
/explore -> {
    Shelves: ExploreShelf[]
}
```
### `/quick?q=<query>`
Returns tracks to be quickly displayed as the user is typing a search
* `<query>`: An incomplete search query
```
/quick?q=<query> -> {
    Tracks: FetchedTrack[]
}
```
