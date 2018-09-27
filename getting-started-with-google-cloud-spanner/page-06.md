6. Load Data

We now have a database called `demo` containing a table called `songs`. Now let's use the Java client library to populate our table of songs with columns for each song name, year of release in the US, and peak chart position. Before you can do reads or writes, we must create a [DatabaseClient](http://googlecloudplatform.github.io/google-cloud-java/latest/apidocs/com/google/cloud/spanner/DatabaseClient.html), which encapsulates a Cloud Spanner database connection.

Open the Cloud Shell, by clicking on the icon highlighted below:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/5f575154c1e91396.png)

Create an empty Maven project:

```
$ mvn -B archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DgroupId=com.google.codelabs \
  -DartifactId=cloudspanner101
```

Next, move into the folder `cloudspanner101` and list its contents:

```
$ cd cloudspanner101 && ls
pom.xml  src
```

Next launch the code editor by clicking on the most left icon on the right side of the cloud shell bar.

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/f35d0781c2a73cf0.png)

Open the `pom.xml` under the `cloudspanner101` folder and add the following 3 dependencies.

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/959419e9547f91e1.png)

This file configures the maven build system to build our application into a jar, including all of our dependencies.

```
<dependency>
   <groupId>com.google.cloud</groupId>
   <artifactId>google-cloud-spanner</artifactId>
   <version>0.35.0-beta</version>
</dependency>
<dependency>
   <groupId>com.google.guava</groupId>
   <artifactId>guava</artifactId>
   <version>23.0</version>
</dependency>
<dependency>
   <groupId>com.google.auth</groupId>
   <artifactId>google-auth-library-oauth2-http</artifactId>
   <version>0.8.0</version>
</dependency>
```

Then add the following <build> section above the <dependencies> section:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/bf033cdb6832a658.png)

```
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
      <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <executions>
              <execution>
                <phase>package</phase>
                <goals>
                  <goal>single</goal>
                </goals>
              </execution>
            </executions>
            <configuration>
              <archive>
                <manifest>
                  <addClasspath>true</addClasspath>
                  <mainClass>com.google.codelabs.App</mainClass>
                </manifest>
              </archive>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
            </configuration>
          </plugin> 
    </plugins>
  </build>
```

Next open the `App.java` with the code editor. It is located in the `src/main/java/com/google/codelabs directory`. Replace the contents of the file with the following code:

```
/*
 * Copyright (C) 2018 Google Inc.
 *
 * Licensed under the Apache License, Version 2.0 (the "License"); you may not
 * use this file except in compliance with the License. You may obtain a copy of
 * the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
 * WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
 * License for the specific language governing permissions and limitations under
 * the License.
 */
package com.google.codelabs;

import com.google.auth.oauth2.GoogleCredentials;
import com.google.cloud.spanner.*;
import org.apache.commons.codec.binary.Hex;

import java.io.IOException;
import java.nio.ByteBuffer;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.UUID;

public class App {

    /**
     * Class to contain Song data.
     */
    static class Song {

        final String songId;
        final String title;
        final int year;
        final int peak;

        Song(String songId, String title, int year, int peak) {
            this.songId = songId;
            this.title = title;
            this.year = year;
            this.peak = peak;
        }
    }

    static final List<Song> SONGS =
        Arrays.asList(
            new Song(getUUID(), "My Bonnie", 1961, 26),
            new Song(getUUID(), "Love Me Do", 1962, 1),
            new Song(getUUID(), "From Me to You", 1963, 116),
            new Song(getUUID(), "She Loves You", 1963, 1),
            new Song(getUUID(), "Roll Over Beethoven", 1963, 68),
            new Song(getUUID(), "I Want to Hold Your Hand", 1963, 1),
            new Song(getUUID(), "Please Please Me", 1964, 3),
            new Song(getUUID(), "All My Loving", 1964, 45),
            new Song(getUUID(), "Why", 1964, 88),
            new Song(getUUID(), "Twist and Shout", 1964, 2),
            new Song(getUUID(), "Can't Buy Me Love", 1964, 1),
            new Song(getUUID(), "Do You Want to Know a Secret", 1964, 2),
            new Song(getUUID(), "Ain't She Sweet", 1964, 19),
            new Song(getUUID(), "A Hard Day's Night", 1964, 1),
            new Song(getUUID(), "I'll Cry Instead", 1964, 25),
            new Song(getUUID(), "And I Love Her", 1964, 12),
            new Song(getUUID(), "Matchbox", 1964, 17),
            new Song(getUUID(), "I Feel Fine", 1964, 1),
            new Song(getUUID(), "Eight Days a Week", 1965, 1),
            new Song(getUUID(), "Ticket to Ride", 1965, 1),
            new Song(getUUID(), "Help", 1965, 1),
            new Song(getUUID(), "Yesterday", 1965, 1),
            new Song(getUUID(), "Boys", 1965, 102),
            new Song(getUUID(), "We Can Work It Out", 1965, 1),
            new Song(getUUID(), "Nowhere Man", 1966, 3),
            new Song(getUUID(), "Paperback Writer", 1966, 1),
            new Song(getUUID(), "Yellow Submarine", 1966, 2),
            new Song(getUUID(), "Penny Lane", 1967, 1),
            new Song(getUUID(), "All You Need Is Love", 1967, 1),
            new Song(getUUID(), "Hello Goodbye", 1967, 1),
            new Song(getUUID(), "Lady Madonna", 1968, 4),
            new Song(getUUID(), "Hey Jude", 1968, 1),
            new Song(getUUID(), "Get Back", 1969, 1),
            new Song(getUUID(), "The Ballad of John and Yoko", 1969, 8),
            new Song(getUUID(), "Something", 1969, 3),
            new Song(getUUID(), "Let It Be", 1970, 1),
            new Song(getUUID(), "The Long and Winding Road", 1970, 1),
            new Song(getUUID(), "Got to Get You into My Life", 1976, 7),
            new Song(getUUID(), "Ob-La-Di, Ob-La-Da", 1976, 49),
            new Song(getUUID(), "Sgt. Pepper's Lonely Hearts Club Band", 1978, 71),
            new Song(getUUID(), "The Beatles Movie Medley", 1982, 12),
            new Song(getUUID(), "Baby It's You", 1995, 67),
            new Song(getUUID(), "Free as a Bird", 1995, 6),
            new Song(getUUID(), "Real Love", 1996, 11));

    static void writeData(DatabaseClient dbClient) {
        List<Mutation> mutations = new ArrayList<>();
        for (Song song : SONGS) {
            mutations.add(
                Mutation.newInsertBuilder("Songs")
                    .set("SongId")
                    .to(song.songId)
                    .set("Title")
                    .to(song.title)
                    .set("Year")
                    .to(song.year)
                    .set("Peak")
                    .to(song.peak)
                    .build());
        }
        dbClient.write(mutations);
    }

    public static void main(String[] args) throws Exception {
        // Authenticating with Google Application Default Credentials
        try {
            GoogleCredentials.getApplicationDefault();
        } catch (IOException e) {
            // Panic and leave ;)
            e.printStackTrace();
            return;
        }
        SpannerOptions options = SpannerOptions.newBuilder().build();
        Spanner spanner = options.getService();
        String instanceId = "cloudspanner-codelab";
        String databaseId = "demo";

        try {
            DatabaseClient dbClient = spanner.getDatabaseClient(DatabaseId.of(options.getProjectId(), instanceId, databaseId));
            writeData(dbClient);
        } finally {
            spanner.close();
        }
    }

    public static String getUUID() {
        return java.util.UUID.randomUUID().toString();
    }
}
```

To build your app run mvn package from the directory where your `pom.xml` is located:

```
$ mvn package
```
![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/61734e11100a9882.png)

Once your jar file is successfully built, run the resulting application in the cloud shell by entering the following command:

```
$ java -jar target/cloudspanner101-1.0-SNAPSHOT-jar-with-dependencies.jar
```

This may take a little while but you can monitor your progress by watching the database contents on the Cloud Console. You can click on the `Songs` table and on the `data` tab as shown below:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/3864dd744f8097ce.png)