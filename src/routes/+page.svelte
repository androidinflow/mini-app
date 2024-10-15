<script>
  import { onMount } from "svelte";
  import { Button } from "$lib/components/ui/button";
  import {
    Card,
    CardContent,
    CardHeader,
    CardTitle,
    CardDescription,
  } from "$lib/components/ui/card";
  import { Separator } from "$lib/components/ui/separator";
  import {
    Table,
    TableBody,
    TableCell,
    TableHead,
    TableHeader,
    TableRow,
  } from "$lib/components/ui/table";
  import {
    Tabs,
    TabsContent,
    TabsList,
    TabsTrigger,
  } from "$lib/components/ui/tabs";
  import { Skeleton } from "$lib/components/ui/skeleton";
  import { User, MapPin, Info, AlertCircle, Flame } from "lucide-svelte";

  let user = null;
  let isReady = false;
  let colorScheme = "light";
  let tg;
  let userLocation = null;
  let locationStatus = "";

  function triggerHapticFeedbackAndRequestLocation() {
    if (tg?.HapticFeedback) {
      tg.HapticFeedback.impactOccurred("medium");
      console.log("Haptic feedback triggered");
    } else {
      console.log("Haptic feedback not available");
    }

    locationStatus = "Requesting location...";

    if (tg?.geolocation) {
      // Telegram WebApp geolocation
      tg.geolocation.getCurrentPosition(
        handleLocationSuccess,
        handleLocationError,
        { timeout: 10000 }
      );
    } else if (navigator.geolocation) {
      // Browser geolocation
      navigator.geolocation.getCurrentPosition(
        handleLocationSuccess,
        handleLocationError,
        { timeout: 10000, enableHighAccuracy: true }
      );
    } else {
      locationStatus = "Geolocation is not supported by this browser/app.";
    }
  }

  function handleLocationSuccess(position) {
    userLocation = {
      latitude: position.coords.latitude,
      longitude: position.coords.longitude,
    };
    locationStatus = "Location received successfully!";
    console.log("Location received:", userLocation);
  }

  function handleLocationError(error) {
    console.error("Error getting location:", error);
    switch (error.code) {
      case error.PERMISSION_DENIED:
        locationStatus = "User denied the request for Geolocation.";
        break;
      case error.POSITION_UNAVAILABLE:
        locationStatus = "Location information is unavailable.";
        break;
      case error.TIMEOUT:
        locationStatus = "The request to get user location timed out.";
        break;
      default:
        locationStatus = "An unknown error occurred.";
        break;
    }
  }

  onMount(() => {
    if (window.Telegram?.WebApp) {
      tg = window.Telegram.WebApp;
      tg.ready();
      user = tg.initDataUnsafe.user;
      isReady = true;
      colorScheme = tg.colorScheme;
    } else {
      isReady = true;
      colorScheme = "light";
    }
  });
</script>

<div class={isReady ? colorScheme : "dark"}>
  <Card class="max-w-3xl mx-auto my-8">
    <CardHeader>
      <CardTitle class="text-3xl font-bold flex items-center">
        <Flame class="w-8 h-8 mr-2 text-red-500" />
        TeleHub
      </CardTitle>
      <CardDescription>Just a demo</CardDescription>
    </CardHeader>
    <CardContent>
      {#if isReady}
        <Tabs defaultValue="user-info" class="w-full">
          <TabsList class="grid w-full grid-cols-2">
            <TabsTrigger
              value="user-info"
              class="flex items-center justify-center"
            >
              <User class="w-5 h-5 mr-2" />
              User Info
            </TabsTrigger>
            <TabsTrigger
              value="location"
              class="flex items-center justify-center"
            >
              <MapPin class="w-5 h-5 mr-2" />
              Location
            </TabsTrigger>
          </TabsList>
          <TabsContent value="user-info">
            {#if user}
              <Card>
                <CardHeader>
                  <CardTitle class="text-2xl">User Information</CardTitle>
                </CardHeader>
                <CardContent>
                  <Table>
                    <TableBody>
                      <TableRow>
                        <TableCell class="font-medium">Name</TableCell>
                        <TableCell
                          >{user.first_name} {user.last_name || ""}</TableCell
                        >
                      </TableRow>
                      <TableRow>
                        <TableCell class="font-medium">Username</TableCell>
                        <TableCell>{user.username || "Not set"}</TableCell>
                      </TableRow>
                      <TableRow>
                        <TableCell class="font-medium">User ID</TableCell>
                        <TableCell>{user.id}</TableCell>
                      </TableRow>
                    </TableBody>
                  </Table>
                </CardContent>
              </Card>
            {:else}
              <Card class="bg-yellow-100 border-yellow-300">
                <CardContent class="flex items-center p-4">
                  <AlertCircle class="w-6 h-6 text-yellow-700 mr-2" />
                  <p class="text-yellow-700">
                    User data not available. Are you running this outside of
                    Telegram?
                  </p>
                </CardContent>
              </Card>
            {/if}
          </TabsContent>
          <TabsContent value="location">
            <Card>
              <CardHeader>
                <CardTitle class="text-2xl">Location Services</CardTitle>
              </CardHeader>
              <CardContent>
                <Button
                  on:click={triggerHapticFeedbackAndRequestLocation}
                  class="w-full mb-4"
                >
                  <MapPin class="w-5 h-5 mr-2" />
                  Get My Location
                </Button>

                {#if locationStatus}
                  <p class="mb-4 text-center font-semibold">{locationStatus}</p>
                {/if}
                {#if userLocation}
                  <div>
                    <p class="text-center mb-2">
                      Latitude: {userLocation.latitude.toFixed(6)}, Longitude: {userLocation.longitude.toFixed(
                        6
                      )}
                    </p>
                    <div
                      class="w-full h-64 rounded-lg overflow-hidden border border-gray-300"
                    >
                      <iframe
                        title="user location"
                        width="100%"
                        height="100%"
                        frameborder="0"
                        scrolling="no"
                        marginheight="0"
                        marginwidth="0"
                        src="https://www.openstreetmap.org/export/embed.html?bbox={userLocation.longitude -
                          0.01}%2C{userLocation.latitude -
                          0.01}%2C{userLocation.longitude +
                          0.01}%2C{userLocation.latitude +
                          0.01}&amp;layer=mapnik&amp;marker={userLocation.latitude}%2C{userLocation.longitude}"
                      ></iframe>
                    </div>
                  </div>
                {/if}
              </CardContent>
            </Card>
          </TabsContent>
        </Tabs>
      {:else}
        <div class="space-y-4">
          <Skeleton class="h-12 w-[250px]" />
          <Skeleton class="h-4 w-[300px]" />
          <Skeleton class="h-4 w-[250px]" />
          <Skeleton class="h-4 w-[200px]" />
          <Skeleton class="h-12 w-full" />
        </div>
      {/if}
    </CardContent>
  </Card>
</div>

<style>
  :global(.light) {
    background-color: var(--tg-theme-bg-color, #f0f2f5);
    color: var(--tg-theme-text-color, #000000);
  }
  :global(.dark) {
    background-color: var(--tg-theme-bg-color, #18191a);
    color: var(--tg-theme-text-color, #ffffff);
  }
</style>
