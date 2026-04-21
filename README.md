# RecognitionMedia WAP Repro

This folder is a small reproduction workspace for testing different `AppVeyor.BuildAgent` versions against a solution that mixes:

- classic ASP.NET Web Application Projects (`Web`, `API`)
- one SDK-style ASP.NET Core project (`CustomerIOService`)

It is designed to exercise the same broad behavior pattern as the RecognitionMedia logs:

- solution build through `msbuild`
- classic WAP packaging through `/p:DeployOnBuild=True /p:PublishProfile=appveyor`
- separate SDK project publish through `dotnet msbuild`

## Layout

- `RecognitionMedia.Repro.sln`
- `Web/Web.csproj` - classic WAP
- `API/API.csproj` - classic WAP
- `CustomerIOService/CustomerIOService.csproj` - SDK-style ASP.NET Core project
- `appveyor.yml`

## Expected Behavior

On a Windows image with the classic web build targets available, `publish_wap: true` should produce:

- `Web.zip`
- `API.zip`

The `after_build` script should additionally produce:

- `CustomerIOService.zip`

If you are comparing build-agent versions, the main thing to watch in the logs is whether the WAP packaging phase emits lines such as:

```text
Creating directory "obj\\Production\\Package\\PackageTmp"
Packaging into ...\\Web.zip
Package "Web.zip" is successfully created
```

If those lines disappear while the solution still reports `0 Error(s)`, the issue is likely in publish/package behavior rather than compilation.

## Suggested AppVeyor Comparison

1. Bake one image with the old build agent.
2. Bake one image with the new build agent.
3. Run the same `appveyor.yml` on both images.
4. Compare:
   - the `msbuild` command line
   - whether `Web.zip` and `API.zip` are created
   - what artifact collection finds near the end of the log

