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

# Server Endpoints
An endpoint may return a single Return Type as a dictionary, or a dictionary containing multiple objects and arrays, as specified.


### `/search?q=<query>`
Returns search results for `<query>`
* `<query>: String` The users' search phrase
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
### `/exact?song=<title>&album=<album title>&artist=<artist name>`
Returns tracks that fuzzy match `<title>`, `<album title>`, and `<artist name>`
* `<title>`: Track title
* `<album title>`: Album title
* `<artist name>`: Artists name, comma seperated
```
/exact?song=<title>&album=<album title>&artist=<artist name> -> {
    Tracks: ImportedTrack[]
}
```
