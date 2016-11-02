# spark-build

O build deve ser feito usando Java 8

Precisamos criar a variavel de ambiente:

```
export MAVEN_OPTS="-Xmx2g -XX:ReservedCodeCacheSize=512m"
```

```bash
./build/mvn -Pyarn -Phadoop-2.7 -Dhadoop.version=2.7.3 -DskipTests --name custom-spark --tgz -Psparkr -Phive -Phive-thriftserver clean package
```

Podemos testar também com os flags: `-Psparkr -Phadoop-provided -Pyarn -DzincPort=3038`

Uma opção mais adequada é usar a shell que cria distribuições. Veja um exemplo:

```bash
./dev/make-distribution.sh --name custom-spark --tgz -Psparkr -Phadoop-2.7 -Phadoop-provided -Phive -Phive-thriftserver -Pyarn
```

Isto gera o arquivo `spark-2.0.1-bin-custom-spark.tgz`. Todo o conteúdo compactado 
dentro do **tgz** fica sob o diretório `spark-2.0.1-bin-custom-spark`.

Se desejar saber mais informações sobre esta shell use:

```bash
./dev/make-distribution.sh --help
```

Atenção: O YARN usa protocolos diferentes entre as versões. Para saber qual o 
protocolo instalado no seu ambiente execute:


```bash
/usr/local/hadoop/bin/yarn version
```

No caso de desejar uma distribuição diferente da versão 2.5.0 use o 
flag `-Dyarn.version=VERSÃO` onde VERSÃO deve ser superior a 2.2.0

**Resumindo:** Usando o comando mostrado abaixo

```bash
./dev/make-distribution.sh --name custom-spark --tgz -Psparkr -Phadoop-2.7 -Phadoop-provided -Phive -Phive-thriftserver -Pyarn
```

geramos o arquivo `spark-2.0.1-bin-custom-spark-hadoop-2.7.3.tgz` 
no diretório corrente, que pode ser expandido assim:

```bash
tar -xzvf spark-2.0.1-bin-custom-spark-hadoop-2.7.3.tgz
```

Após a expansão você poderá conferir o arquivo `spark-2.0.1-bin-custom-spark/RELEASE`
usando o comando `cat` 

```bash
cat spark-2.0.1-bin-custom-spark/RELEASE

Spark 2.0.1 (git revision 0304b85) built for Hadoop 2.7.3
Build flags: -Psparkr -Phadoop-2.7 -Phive -Phive-thriftserver -Pyarn
```
Para Instalar no host desejado copie o arquivo da distribuição gerada para
o diretório `/tmp` do host e faça o seguinte:

```bash
sudo tar -C /usr/local/ -xzf /tmp/spark-2.0.1-bin-custom-spark-hadoop-2.7.3.tgz && \
du -k /usr/local/spark-2.0.1-bin-custom-spark | sort -n | tail && \
sudo rm -rf /usr/local/spark && \
sudo mv /usr/local/spark-2.0.1-bin-custom-spark /usr/local/spark && \
sudo chown -R hduser:hadoop /usr/local/spark 
```
