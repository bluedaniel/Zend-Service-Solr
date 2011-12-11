
Example Usage - Solr model (Plus zend pagination)
-

    class Model_Solr {

        public $_pagination = null;

        /*
         * Pass in params like 'q', 'page', 'count'
         *
         * /
        public function search($op = array()) {	

            $pagination_array = array();

            $solr = $this->get_solr_connection();
            if ($solr->ping ()) {

                $params = array(
                    'facet' => 'on',
                    'facet.field' => 'cat', // cat - Short for category
                    'hl' => 'on',
                    'hl.fl' => 'name',
                    'spellcheck' => 'true',
                );

                $offset = ($op['page'] - 1) * $op['count'] + 0;

                $query = 'manufacturer:'.$op['q'];

                $results = $solr->search($query, $offset, $op['count'], $params);

                $output['response'] = $this->object_to_array($results->response);
                $output['responseHeader'] = $this->object_to_array($results->responseHeader);
                $output['facet_counts'] = $this->object_to_array($results->facet_counts);

                $pagination_array = array();
                if(isset($output['response']['numFound'])) {
                    $i=1;
                    while($i <= $output['response']['numFound']) {
                        $pagination_array[] = $i;
                        $i++;
                    }
                }
            }
            $pagination = Zend_Paginator::factory ( $pagination_array );
            $pagination->setCurrentPageNumber ( $op['page'] );
            $pagination->setItemCountPerPage ( 10 );
            $pagination->setPageRange ( 7 );

            $this->_pagination = $pagination;

            return $output;

        }


        public function returnPagination() {
            return $this->_pagination;
        }

        /**
         * 
         * Get spell
         * @param arr $op
         */
        public function spell($op = array()) {		
            $solr = $this->get_solr_connection();	
            if ($solr->ping ()) {

                $results = $solr->spell($op['q']);
                $results = json_decode($results, true);

                return $results['spellcheck'];
            }
        }

        private function get_solr_connection() {
            return new Zend_Service_Solr ('HOST', 'PORT', 'PATH'); // Configure
        }

        private function object_to_array($object) {
            if(is_array($object) || is_object($object)) {
                $result = array();
                foreach($object as $key => $value) {
                    $result[$key] = $this->object_to_array($value);
                }
                return $result;
            }
            return $object;
        }

    }