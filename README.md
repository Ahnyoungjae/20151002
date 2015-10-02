# 20151002 하둡 설치/실행 하기


하둡 다운로드 후
vi $HOME/.bashrc <--들어가서

    # 아래 11개 export 만 추가해주면 됨
    export JAVA_HOME=/usr/java/jdk1.8
    export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
    export HADOOP_HOME=/home/hadoop/hadoop-2.7.1
    export HADOOP_INSTALL=$HADOOP_HOME
    export HADOOP_MAPRED_HOME=$HADOOP_HOME
    export HADOOP_COMMON_HOME=$HADOOP_HOME
    export HADOOP_HDFS_HOME=$HADOOP_HOME
    export HADOOP_YARN_HOME=$HADOOP_HOME
    export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
    export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
    export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH

# (하둡을 구동하는 스크립트에서 사용되는 환경 변수)
    vi $HADOOP_HOME/etc/hadoop/hadoop-env.sh 
    export JAVA_HOME=/usr/java/jdk1.8

# Hadoop 설치 후 로그파일, 네트워크 튜닝, I/O튜닝, 파일 시스템 튜닝, 압축 등과 같이 기본적인 하부 시스템 설정
    # 맵리듀스에서도 공통으로 사용

    vi $HADOOP_HOME/etc/hadoop/core-site.xml

    <configuration> 
        <property>
            <name>fs.default.name</name>
            <value>hdfs://localhost:9000</value>
        </property>
    </configuration> 


    # (네임노드, 보조 네임노드, 데이터노드 등과 같은 HDFS 데몬을 위한 환경 설정 구성)
    vi $HADOOP_HOME/etc/hadoop/hdfs-site.xml

    <configuration> 
        <property>
            <name>dfs.replication</name>
            <value>1</value>
        </property>

        <property>
            <name>dfs.name.dir</name>
            <value>file:///home/hadoop/hadoopdata/hdfs/namenode</value>
        </property>

        <property>
            <name>dfs.data.dir</name>
            <value>file:///home/hadoop/hadoopdata/hdfs/datanode</value>
        </property>
    </configuration>         


    cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml



    # (잡트래커와 테스크트래커와 같은 맵리듀스 데몬을 위한 환경 설정 구성)
    vi  $HADOOP_HOME/etc/hadoop/mapred-site.xml

    <configuration> 
        <property>
            <name>mapreduce.framework.name</name>
            <value>yarn</value>
        </property>
    </configuration> 


    vi $HADOOP_HOME/etc/hadoop/yarn-site.xml

    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

7. 하둡시작


    hdfs namenode -format
    start-dfs.sh
    start-yarn.sh
word counting example


    # 테스트 문서 제작
    vim test.txt

    # 아래 두줄을 test.txt 에 넣어주면 됨
    # 테스트용 문서를 옮겨놓고 사용해도 됨)
    i am a boy
    you are a girl

    hdfs dfs -mkdir /user   # user 디렉토리를 만듬 # exist directory 인 경우는 패스하면 됨
    hdfs dfs -mkdir /user/hadoop   # hadoop 디렉토리를 만듬 # exist directory 인 경우는 패스하면 됨
    hdfs dfs -mkdir /user/hadoop/input   # input 디렉토리를 만듬
    hdfs dfs -put test.txt /user/hadoop/input   # test.txt 파일을 input 디렉토리에 넣음

    # Word Count java 코드 작성( github 에서 WordCount.java 파일 다운로드)
    wget https://raw.githubusercontent.com/kowonsik/CCL/master/WordCount.java

    # WordCount.java compile
    hadoop com.sun.tools.javac.Main WordCount.java

    # jar 파일 만들기
    jar -cf wc.jar WordCount*.class

    # Word Count 실행
    hadoop jar wc.jar WordCount /user/hadoop/input /output

    # 기본 파일 명령어
    hadoop fs -ls /   # 모든 디렉토리 확인
    hadoop fs -ls /output   # output 디렉토리 확인

    # 결과 확인
    hdfs dfs -cat /output/part-r-00000

결과값 
a	2
am	1
are	1
boy	1
girl	1
i	1
you	1
