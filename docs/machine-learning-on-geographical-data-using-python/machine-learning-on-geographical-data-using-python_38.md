# making the line string into a list of the coordinates as strings and removing redundant information
list_of_points_extracted = str(row['geometry']).strip('LINESTRING Z (').strip(')').split(',')
list_of_points_extracted = [point[:-2] for point in list_of_points_extracted]
