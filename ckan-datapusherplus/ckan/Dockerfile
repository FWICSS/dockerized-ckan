FROM ckan/ckan-base:2.10.3

ENV CKAN__VIEWS__DEFAULT_VIEWS="image_view pdf_view datatables_view text_view recline_view recline_grid_view recline_graph_view pdf_view geojson_view officedocs_view"
ENV CKAN__MAX_RESOURCE_SIZE=100
ENV CKAN__DATAPUSHER__FORMATS ="csv xls xlsx tsv application/csv application/vnd.ms-excel application/vnd. openxmlformats-officedocument.spreadsheetml.sheet ods application/vnd.oasis.opendocument. spreadsheet application/json geojson application/vnd.geo+json application/zip shp kml kmz"
ENV CKAN__AUTH__CREATE_USER_VIA_API = false
ENV CKAN__AUTH__CREATE_USER_VIA_WEB = false
ENV CKAN__AUTH__PUBLIC_USER_DETAILS = false
# Install any extensions needed by your CKAN instance
# See Dockerfile.dev for more details and examples

### Spatial ###
### Spatial extension ###
RUN apk update && apk add python3-dev libxml2-dev libxslt-dev geos geos-dev proj proj-dev proj-util py3-shapely libmagic py3-magic
# python3-virtualenv python3-pip python3-wheel build-essential libxslt1-dev zlib1g-dev git libffi-dev libpq-dev file
RUN pip install --upgrade pip
RUN pip install python-magic
#RUN pip install -e "git+https://github.com/ckan/ckanext-spatial.git#egg=ckanext-spatial"
#RUN pip install -r ${APP_DIR}/src/ckanext-spatial/requirements.txt


### dataJSON ###
RUN pip install -e "git+https://github.com/GSA/ckanext-datajson.git#egg=ckanext-datajson"
RUN pip install -r ${APP_DIR}/src/ckanext-datajson/requirements.txt

### Geoview ###
RUN pip3 install -e git+https://github.com/ckan/ckanext-geoview.git#egg=ckanext-geoview

### Office Docs ###
RUN pip3 install -e git+https://github.com/jqnatividad/ckanext-officedocs.git#egg=ckanext-officedocs


### MAP ###
#RUN pip install -e "git+https://github.com/ckan/ckanext-mapviews.git#egg=ckanext-mapviews"


# RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.plugins = ${CKAN__PLUGINS}"
RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.views.default_views = ${CKAN__VIEWS__DEFAULT_VIEWS}"
RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.max_resource_size = ${CKAN__MAX_RESOURCE_SIZE}"
RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.datapusher.formats = ${CKAN__DATAPUSHER__FORMATS}"
RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.auth.create_user_via_api = ${CKAN__AUTH__CREATE_USER_VIA_API}"
RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.auth.create_user_via_web = ${CKAN__AUTH__CREATE_USER_VIA_WEB}"
RUN ckan config-tool ${APP_DIR}/ckan.ini "ckan.auth.public_user_details = ${CKAN__AUTH__PUBLIC_USER_DETAILS}"

# Copy custom initialization scripts
COPY docker-entrypoint.d/* /docker-entrypoint.d/

# Apply any patches needed to CKAN core or any of the built extensions (not the
# runtime mounted ones)
COPY patches ${APP_DIR}/patches

RUN for d in $APP_DIR/patches/*; do \
        if [ -d $d ]; then \
            for f in `ls $d/*.patch | sort -g`; do \
                cd $SRC_DIR/`basename "$d"` && echo "$0: Applying patch $f to $SRC_DIR/`basename $d`"; patch -p1 < "$f" ; \
            done ; \
        fi ; \
    done
