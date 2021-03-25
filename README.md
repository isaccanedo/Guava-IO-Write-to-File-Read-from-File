## Guava IO - Gravar em Arquivo, Ler de Arquivo

# 1. Visão Geral
Neste tutorial, aprenderemos como escrever em um arquivo e, em seguida, como ler um arquivo usando Guava IO. Discutiremos como gravar em um arquivo.

# 2. Gravar usando arquivos
Vamos começar com um exemplo simples para gravar uma String em um arquivo usando Arquivos:

```
@Test
public void whenWriteUsingFiles_thenWritten() throws IOException {
    String expectedValue = "Hello world";
    File file = new File("test.txt");
    Files.write(expectedValue, file, Charsets.UTF_8);
    String result = Files.toString(file, Charsets.UTF_8);
    assertEquals(expectedValue, result);
}
```

Observe que também podemos anexar a um arquivo existente usando a API Files.append().

# 3. Grave em arquivo usando CharSink
A seguir - vamos ver como escrever uma String em um arquivo usando CharSink. No exemplo a seguir - obtemos um CharSink de um arquivo usando Files.asCharSink() e então o usamos para escrever:

```
@Test
public void whenWriteUsingCharSink_thenWritten() throws IOException {
    String expectedValue = "Hello world";
    File file = new File("test.txt");
    CharSink sink = Files.asCharSink(file, Charsets.UTF_8);
    sink.write(expectedValue);

    String result = Files.toString(file, Charsets.UTF_8);
    assertEquals(expectedValue, result);
}
```

Também podemos usar o CharSink para escrever várias linhas em um arquivo. No exemplo a seguir - escrevemos uma Lista de nomes e usamos um espaço como separador de linha:

```
@Test
public void whenWriteMultipleLinesUsingCharSink_thenWritten() throws IOException {
    List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
    File file = new File("test.txt");
    CharSink sink = Files.asCharSink(file, Charsets.UTF_8);
    sink.writeLines(names, " ");

    String result = Files.toString(file, Charsets.UTF_8);
    String expectedValue = Joiner.on(" ").join(names);
    assertEquals(expectedValue, result.trim());
}
```

# 4. Gravar em arquivo usando ByteSink
Também podemos escrever bytes brutos usando ByteSink. No exemplo a seguir - obtemos um ByteSink de um arquivo usando Files.asByteSink() e então o usamos para escrever:

```
@Test
public void whenWriteUsingByteSink_thenWritten() throws IOException {
    String expectedValue = "Hello world";
    File file = new File("test.txt");
    ByteSink sink = Files.asByteSink(file);
    sink.write(expectedValue.getBytes());

    String result = Files.toString(file, Charsets.UTF_8);
    assertEquals(expectedValue, result);
}
```

Observe que podemos mover entre um ByteSink e um CharSink usando a conversão simples byteSink.asCharSink().

# 5. Leia o arquivo usando arquivos
A seguir - vamos discutir como ler um arquivo usando arquivos.

No exemplo a seguir - lemos todo o conteúdo de um arquivo usando o simples Files.toString():

```
@Test
public void whenReadUsingFiles_thenRead() throws IOException {
    String expectedValue = "Hello world";
    File file = new File("test.txt");
    String result = Files.toString(file, Charsets.UTF_8);

    assertEquals(expectedValue, result);
}
```

Também podemos ler o arquivo em uma Lista de linhas, como no exemplo a seguir:

```
@Test
public void whenReadMultipleLinesUsingFiles_thenRead() throws IOException {
    File file = new File("test.txt");
    List<String> result = Files.readLines(file, Charsets.UTF_8);

    assertThat(result, contains("John", "Jane", "Adam", "Tom"));
}
```

Observe que podemos usar Files.readFirstLine() para ler apenas a primeira linha de um arquivo.

# 6. Leia o arquivo usando CharSource
A seguir - vamos ver como ler um arquivo usando Charsource.

No exemplo a seguir - obtemos um CharSource de um arquivo usando Files.asCharSource() e então o usamos para ler todo o conteúdo do arquivo usando read():

```
@Test
public void whenReadUsingCharSource_thenRead() throws IOException {
    String expectedValue = "Hello world";
    File file = new File("test.txt");
    CharSource source = Files.asCharSource(file, Charsets.UTF_8);

    String result = source.read();
    assertEquals(expectedValue, result);
}
```

Também podemos concatenar dois CharSources e usá-los como um CharSource.

No exemplo a seguir - lemos dois arquivos, o primeiro contém “Hello world” e o outro contém “Test“:

```
@Test
public void whenReadMultipleCharSources_thenRead() throws IOException {
    String expectedValue = "Hello worldTest";
    File file1 = new File("test1.txt");
    File file2 = new File("test2.txt");

    CharSource source1 = Files.asCharSource(file1, Charsets.UTF_8);
    CharSource source2 = Files.asCharSource(file2, Charsets.UTF_8);
    CharSource source = CharSource.concat(source1, source2);

    String result = source.read();
    assertEquals(expectedValue, result);
}
```

# 7. Ler do arquivo usando CharStreams
Agora - vamos ver como ler o conteúdo de um arquivo em uma string usando CharStreams, por meio de um FileReader intermediário:

```
@Test
public void whenReadUsingCharStream_thenRead() throws IOException {
    String expectedValue = "Hello world";
    FileReader reader = new FileReader("test.txt");
    String result = CharStreams.toString(reader);

    assertEquals(expectedValue, result);
    reader.close();
}
```

# 8. Leia o arquivo usando ByteSource
Podemos usar ByteSource para o conteúdo do arquivo no formato de byte bruto - como no exemplo a seguir:

```
@Test
public void whenReadUsingByteSource_thenRead() throws IOException {
    String expectedValue = "Hello world";
    File file = new File("test.txt");
    ByteSource source = Files.asByteSource(file);

    byte[] result = source.read();
    assertEquals(expectedValue, new String(result));
}
```

Também podemos começar a ler bytes após o deslocamento específico usando slice() como no exemplo a seguir:

```
@Test
public void whenReadAfterOffsetUsingByteSource_thenRead() throws IOException {
    String expectedValue = "lo world";
    File file = new File("test.txt");
    long offset = 3;
    long len = 1000;

    ByteSource source = Files.asByteSource(file).slice(offset, len);
    byte[] result = source.read();
    assertEquals(expectedValue, new String(result));
}
```

Observe que podemos usar byteSource.asCharSource() para obter uma visualização CharSource deste ByteSource.

# 9. Ler do arquivo usando ByteStreams
A seguir - vamos ver como ler o conteúdo de um arquivo em uma matriz de bytes bruta usando ByteStreams; usaremos um FileInputStream intermediário para realizar a conversão:

```
@Test
public void whenReadUsingByteStream_thenRead() throws IOException {
    String expectedValue = "Hello world";
    FileInputStream reader = new FileInputStream("test.txt");
    byte[] result = ByteStreams.toByteArray(reader);
    reader.close();

    assertEquals(expectedValue, new String(result));
}
```

# 10. Leia Usando Recursos
Finalmente - vamos ver como ler arquivos que existem no classpath - usando o utilitário Resources como no exemplo a seguir:

```
@Test
public void whenReadUsingResources_thenRead() throws IOException {
    String expectedValue = "Hello world";
    URL url = Resources.getResource("test.txt");
    String result = Resources.toString(url, Charsets.UTF_8);

    assertEquals(expectedValue, result);
}
```

# 11. Conclusão
Neste tutorial rápido, ilustramos as várias maneiras de ler e gravar arquivos usando o suporte e os utilitários do Guava IO.

