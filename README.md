"# Week9Poroject" 
/** 
video: https://drive.google.com/file/d/1VjcRN-qJweMzTf4nu-shNUxfBeZqTv_M/view?usp=sharing
 * 
 */
package Project7.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.time.LocalTime;
import java.util.List;

import Project7.entity.Project;
import Project7.exception.DbException;
import provided.util.DaoBase;
/**
 * @author Jose
 *
 */





public class ProjectsDao extends DaoBase {
	
	private static final String MATERIAL_TABLE = "material";
	private static final String PROJECT_TABLE = "project";
	private static final String STEPS_TABLE = "STEPS";
	private static final String CATEGORY_TABLE = "category";
	private static final String PROJECT_CATEGORY = "project_category";


	



	public Project insertProject(Project project) {
		String sql = ""
			+ "INSERT INTO " + PROJECT_TABLE + " " 
			+ "(project_name, notes, difficulty, estimated_time, actual_time) "
			+ "VALUES"
			+ "(?, ?, ?, ?, ?)";
		
		try(Connection conn = DbConnection.getConnection()) {
			startTransaction(conn);
		try(PreparedStatement stmt = conn.prepareStatement(sql)) {
			setParameter(stmt, 1, project.getProjectsName(), String.class);
			setParameter(stmt, 2, project.getNotes(), String.class);
			setParameter(stmt, 3, project.getDifficulty(), Integer.class);
			setParameter(stmt, 4, project.getEstimatedHours(), LocalTime.class);
			setParameter(stmt, 5, project.getActualHours(), LocalTime.class);
			
			stmt.executeUpdate();
			Integer projectId = getLastInsertId(conn, PROJECT_TABLE);
			
			commitTransaction(conn);
			
			project.setProject_id(projectId);
			return project;
			
			
		}
		catch(Exception e) {
			rollbackTransaction(conn);
			throw new DbException(e);
		}
		} catch (Exception e) {
			throw new DbException(e);
			
		}
	}
	
	
	
	public void executeBatch(List<String> sqlBatch) {
		try(Connection conn= DbConnection.getConnection()) {
			startTransaction(conn);
			
			try(Statement stmt = conn.createStatement()) {
				for(String sql : sqlBatch) {
					stmt.addBatch(sql);
				}
				stmt.executeBatch();
				commitTransaction(conn);
			}
			catch(Exception e) {
				rollbackTransaction(conn);
				throw new DbException(e);
			}
		
		} catch (SQLException e) {
		throw new DbException(e);
		
			
		}
		
	}

}


package Project7.service;

import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.LinkedList;
import java.util.List;

import Project7.dao.ProjectsDao;
import Project7.entity.Project;
import Project7.exception.DbException;

public class ProjectsService {
	
private static final String SCHEMA_FILE = "projects_schema.sql";
private static final String DATA_FILE = "projects_data.sql";
	
private ProjectsDao ProjectsDAO = new ProjectsDao();
	
	public void createAndPopulateTables() {
		loadFromFile(SCHEMA_FILE);
		loadFromFile (DATA_FILE);
		
		
		
	}

	private void loadFromFile(String fileName) {
		String content = readFileContent (fileName);
		List<String> sqlStatements = convertContentToSqlStatements(content);
		
		
		ProjectsDAO.executeBatch(sqlStatements);
		
	}

	private List<String> convertContentToSqlStatements(String content) {
		content = removeComments(content);
		content = replaceWhitespaceSequencesWithSingleSpace(content);
		
		return extractLinesFromContent(content);
		
	}

	private List<String> extractLinesFromContent(String content) {
		List<String> lines = new LinkedList<>();
		
		while(!content.isEmpty()) {
			int semicolon = content.indexOf(";");
			if (semicolon == -1) {
				if(!content.isBlank()) {
					lines.add(content);
					
				}
				content = "";
			}
			else {
				lines.add(content.substring(0 , semicolon).trim());
				content = content.substring(semicolon + 1);
			}
		}
		return lines;

		
	}
	
	

	private String replaceWhitespaceSequencesWithSingleSpace(String content) {
		
		return content.replaceAll("\\s+", " ");
	}

	private String removeComments(String content) {
		StringBuilder builder = new StringBuilder(content);
		int commentPos = 0;
		
		while((commentPos = builder.indexOf("-- ", commentPos)) != -1) {
			int eolPos = builder.indexOf("\n", commentPos +1);
			
			if(eolPos == -1) {
				builder.replace(commentPos, builder.length(), "");
				
			}
			else {
				builder.replace(commentPos, eolPos + 1, "");
			}
			
		}
		
		return builder.toString();
	}

	private String readFileContent(String fileName) {
		try {
		
		Path path = Paths.get(getClass().getClassLoader().getResource(fileName).toURI());
		return Files.readString(path);
		
	} catch(Exception e) {
		
		throw new DbException(e);
	}
	
	}
	public static void main(String[] args) {
		new ProjectsService().createAndPopulateTables();
	}

	public Project addProject(Project project) {
	
		return ProjectsDAO.insertProject(project);
	}
}


package Project7.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;
import java.time.LocalTime;
import java.util.List;

import Project7.entity.Project;
import Project7.exception.DbException;
import provided.util.DaoBase;
/**
 * @author Jose
 *
 */





public class ProjectsDao extends DaoBase {
	
	private static final String MATERIAL_TABLE = "material";
	private static final String PROJECT_TABLE = "project";
	private static final String STEPS_TABLE = "STEPS";
	private static final String CATEGORY_TABLE = "category";
	private static final String PROJECT_CATEGORY = "project_category";


	



	public Project insertProject(Project project) {
		String sql = ""
			+ "INSERT INTO " + PROJECT_TABLE + " " 
			+ "(project_name, notes, difficulty, estimated_time, actual_time) "
			+ "VALUES"
			+ "(?, ?, ?, ?, ?)";
		
		try(Connection conn = DbConnection.getConnection()) {
			startTransaction(conn);
		try(PreparedStatement stmt = conn.prepareStatement(sql)) {
			setParameter(stmt, 1, project.getProjectsName(), String.class);
			setParameter(stmt, 2, project.getNotes(), String.class);
			setParameter(stmt, 3, project.getDifficulty(), Integer.class);
			setParameter(stmt, 4, project.getEstimatedHours(), LocalTime.class);
			setParameter(stmt, 5, project.getActualHours(), LocalTime.class);
			
			stmt.executeUpdate();
			Integer projectId = getLastInsertId(conn, PROJECT_TABLE);
			
			commitTransaction(conn);
			
			project.setProject_id(projectId);
			return project;
			
			
		}
		catch(Exception e) {
			rollbackTransaction(conn);
			throw new DbException(e);
		}
		} catch (Exception e) {
			throw new DbException(e);
			
		}
	}
	
	
	
	public void executeBatch(List<String> sqlBatch) {
		try(Connection conn= DbConnection.getConnection()) {
			startTransaction(conn);
			
			try(Statement stmt = conn.createStatement()) {
				for(String sql : sqlBatch) {
					stmt.addBatch(sql);
				}
				stmt.executeBatch();
				commitTransaction(conn);
			}
			catch(Exception e) {
				rollbackTransaction(conn);
				throw new DbException(e);
			}
		
		} catch (SQLException e) {
		throw new DbException(e);
		
			
		}
		
	}

 package Project7.entity;

import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.util.List;
import java.util.Objects;
import java.util.LinkedList;
/**
 * @author Jose
 *
 */
public class Project {
private Integer project_id;
private String projectsName;
private LocalTime estimatedHours;
@Override
public String toString() {
 DateTimeFormatter fmt = DateTimeFormatter.ofPattern("dd-MM-YYYY HH:mm");
 String createTime = Objects.nonNull(createdAt) ? fmt.format(createdAt) : "(null)";
	
 String project = "";
 
 project += "\n    ID=" + project_id;
 project += "\n    projectName" + projectsName;
 project += "\n    notes=" + notes;
 project += "\n    estimatedTime=" + estimatedHours;
 project += "\n    actualTime=" + actualHours;
 project += "\n    createdAt=" + createTime;
 
 project += "\n    material:";
 
 for(Materials material : material) {
	 project += "\n     " + material;
		 
 }
 project += "\n    Steps:";
 
 for(Step step :steps) {
	 project += "\n     " + step;
	 
 }
 
project += "\n    Categories:";
 
 for(Category category : categories) {
	 project += "\n     " + category;
	 
 }
 
 return project;
	
}
private LocalTime actualHours;
private Integer difficulty;
private String notes;
private LocalDateTime createdAt;

private List<Materials> material = new LinkedList<>();
private List<Step> steps = new LinkedList<>();
private List<Category> categories = new LinkedList<>();



public Integer getProject_id() {
	return project_id;
}
public void setProject_id(Integer project_id) {
	this.project_id = project_id;
}
public String getProjectsName() {
	return projectsName;
}
public void setProjectsName(String projectsName) {
	this.projectsName = projectsName;
}
public LocalTime getEstimatedHours() {
	return estimatedHours;
}
public void setEstimatedHours(LocalTime estimatedHours) {
	this.estimatedHours = estimatedHours;
}
public LocalTime getActualHours() {
	return actualHours;
}
public void setActualHours(LocalTime actualHours) {
	this.actualHours = actualHours;
}
public Integer getDifficulty() {
	return difficulty;
}
public void setDifficulty(Integer difficulty) {
	this.difficulty = difficulty;
}
public String getNotes() {
	return notes;
}
public void setNotes(String notes) {
	this.notes = notes;
}
public LocalDateTime getCreatedAt() {
	return createdAt;
}
public void setCreatedAt(LocalDateTime createdAt) {
	this.createdAt = createdAt;
}
public List<Category> getCategories() {
	return categories;
}
public void setCategories(List<Category> categories) {
	this.categories = categories;
}
public List<Materials> getMaterial() {
	return material;
}
public List<Step> getSteps() {
	return steps;
}



	
}

}




