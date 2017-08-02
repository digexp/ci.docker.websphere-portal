###########################################################################
# (C) Copyright IBM Corporation 2017.                                     #
#                                                                         #
# Licensed under the Apache License, Version 2.0 (the "License");         #
# you may not use this file except in compliance with the License.        #
# You may obtain a copy of the License at                                 #
#                                                                         #
#      http://www.apache.org/licenses/LICENSE-2.0                         #
#                                                                         #
# Unless required by applicable law or agreed to in writing, software     #
# distributed under the License is distributed on an "AS IS" BASIS,       #
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.#
# See the License for the specific language governing permissions and     #
# limitations under the License.                                          #
###########################################################################

FROM centos:7

#URL from where to download installation media
ARG URL

ARG INSTALLBASE=/tmp
RUN yum -y install \
    ftp \
    ksh \
    less \
    openssl \
    unzip \
    wget && \
    yum -y update && \
    yum clean all

# Copy the install images downloaded from passport advantage into the container
RUN cd $INSTALLBASE && \
    wget -q $URL/WS_PORTAL_ENABLE_SU_V9.0_PART1_MP.zip && \
    unzip WS_PORTAL_ENABLE_SU_V9.0_PART1_MP.zip && \
    rm -rf WS_PORTAL_ENABLE_SU_V9.0_PART1_MP.zip 

# Install IBM Installation Manager
RUN $INSTALLBASE/SETUP/IIM/linux_x86_64/installc \
    -acceptLicense \
    -accessRights nonAdmin \
    -installationDirectory "/opt/IBM/InstallationManager"  \
    -dataLocation "/var/ibm/InstallationManager" \
    -showProgress \
    -preferences com.ibm.cic.common.core.preferences.preserveDownloadedArtifacts=false

RUN rm -rf $INSTALLBASE/SETUP

# Copy the response file
COPY ./*.xml /root/

# Install Portal with a response file - This installs Portal 85 onto WAS 90 with JDK 8
RUN /opt/IBM/InstallationManager/eclipse/tools/imcl \
    -acceptLicense \
    input /root/Portal85BaseResponse.xml \
    -showProgress \
    -log $INSTALLBASE/Install.Portal85Base.log

# Update Portal to CF13 # Copy the response file # Install Portal with a response file
RUN /opt/IBM/InstallationManager/eclipse/tools/imcl \
    -acceptLicense \
    input /root/Portal85CF13Response.xml \
    -showProgress \
    -log $INSTALLBASE/Install.PortalCF13.log

# Run applyCF.sh to actually apply the maintanance
RUN /opt/IBM/WebSphere/wp_profile/PortalServer/bin/applyCF.sh \
    -DWasPassword=wpsadmin \
    -DPortalAdminPwd=wpsadmin

# Install Portal 90 with a response file
RUN /opt/IBM/InstallationManager/eclipse/tools/imcl \
    -acceptLicense \
    input /root/Portal90Response.xml \
    -showProgress \
    -log $INSTALLBASE/Install.Portal90.log

RUN rm -rf $INSTALLBASE/SETUP

EXPOSE 10032 10033 10034 10035 10036 10037 10038 10039 10040 10041 10042 10200 10202 10202 10203

CMD /bin/bash
