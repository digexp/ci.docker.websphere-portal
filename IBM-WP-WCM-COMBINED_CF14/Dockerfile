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

FROM portal:v90

RUN yum -y install \
    unzip \
    less \
    ksh \
    openssl \
    ftp && \
    yum -y update && \
    yum clean all

# Copy the response file
COPY ./*.xml /root/

# Install Portal CF14 with a response file 
RUN /opt/IBM/InstallationManager/eclipse/tools/imcl \
    -acceptLicense \
    input /root/update90response.xml \
    -showProgress \
    -log /tmp/Install.log

RUN /opt/IBM/WebSphere/wp_profile/PortalServer/bin/applyCF.sh \
    -DWasPassword=wpsadmin \
    -DPortalAdminPwd=wpsadmin

EXPOSE 10032 10033 10034 10035 10036 10037 10038 10039 10040 10041 10042 10200 10202 10202 10203

CMD /bin/bash
