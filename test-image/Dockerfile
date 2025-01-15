FROM codercom/code-server
# Based on https://github.com/lim-rachel/java-code-server/blob/main/Dockerfile

USER root
RUN apt-get update -y
RUN apt-get install -y build-essential
RUN apt-get install -y python3 g++ make
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y vim curl wget nano git openssh-client
RUN git clone https://github.com/ucsd-cse230/00-lean-induction.git

USER 1000
